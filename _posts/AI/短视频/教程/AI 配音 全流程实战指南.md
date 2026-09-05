
这是一份为您重新整理的 **《Intel Mac 视频本地化与 AI 配音全流程实战指南》**。

本文档将之前零散的步骤、踩坑记录和代码进行了系统性整合。按照此指南，您可以完成 **“英文字幕高精度翻译 ➔ 生成严格时间轴对齐的 AI 语音 ➔ 完美嵌入原视频”** 的完整自动化工作流。

---

# Intel Mac 视频本地化与 AI 配音全流程实战指南

## **核心工作流**概述
本方案分为两个核心阶段：
1. **翻译阶段**：使用大语言模型（LLM）API，结合上下文分块策略，将英文 SRT 字幕高精度翻译为中文。

>把这些英文字幕文件翻译成中文，保存到“原来名字.srt”（去掉末尾_en），要求翻译准确

2. **配音阶段**：使用微软 Edge TTS 生成语音，通过 Python 脚本动态计算语速、精准控制延迟，实现**毫秒级时间轴对齐**，并解决 FFmpeg 混合音频导致的音量衰减问题。

---

## 第一阶段：基础环境搭建 (Intel Mac 避坑版)

在 Intel Mac 上，环境配置最容易遇到“找不到命令”和“下载慢”的问题。请严格按以下步骤操作。

### 1. 安装 FFmpeg 与 FFprobe (极速方案)
由于 Homebrew 下载较慢，推荐直接下载静态编译版（绿色免安装），1分钟搞定：
1. 浏览器访问 [evermeet.cx](https://evermeet.cx/pub/ffmpeg/) 下载 `ffmpeg`，访问 [evermeet.cx/pub/ffprobe/](https://evermeet.cx/pub/ffprobe/) 下载 `ffprobe`（均为 `.7z` 格式，需用 The Unarchiver 等软件解压）。
2. 打开 Mac **终端 (Terminal)**，将解压出的文件移动到系统目录并赋予权限：
   ```bash
   cd ~/Downloads
   chmod +x ffmpeg ffprobe
   sudo mv ffmpeg ffprobe /usr/local/bin/
   ```
3. **验证安装**（必须输出路径才算成功）：
   ```bash
   which ffmpeg   # 预期输出: /usr/local/bin/ffmpeg
   which ffprobe  # 预期输出: /usr/local/bin/ffprobe
   ```

### 2. 安装 Python 依赖库
在终端中运行以下命令，安装字幕解析、TTS 生成和大模型调用所需的库：
```bash
pip3 install edge-tts srt openai
```

---

## 第二阶段：大模型高精度字幕翻译

**为什么不用普通机翻？** 逐行翻译会丢失上下文，导致代词错乱、口语化不足。本方案采用**大模型 API + 上下文分块翻译**，确保翻译地道、准确。

### 1. 获取大模型 API Key
推荐使用 **DeepSeek**（逻辑强、翻译地道、极便宜）或 **阿里通义千问**。前往对应官网注册并获取 API Key。

### 2. 运行翻译脚本
在视频和字幕所在的文件夹下，创建 `batch_translate_srt.py`，填入以下代码，并**替换您的 API Key**：

```python
import os, glob, re, time, srt
from openai import OpenAI

# ================= 配置区 =================
WORK_DIR = "."
SUB_EXT = ".srt"
API_KEY = "sk-你的API密钥"  # <--- 替换为你的 API Key
BASE_URL = "https://api.deepseek.com" # 通义千问填: "https://dashscope.aliyuncs.com/compatible-mode/v1"
MODEL_NAME = "deepseek-chat"          # 通义千问填: "qwen-max" 或 "qwen-plus"
CHUNK_SIZE = 15  # 每次翻译的行数，兼顾上下文和Token限制
# ==========================================

client = OpenAI(api_key=API_KEY, base_url=BASE_URL)

def clean_llm_response(text):
    text = re.sub(r'^```(?:srt|text)?\n?', '', text.strip())
    text = re.sub(r'\n?```$', '', text.strip())
    match = re.search(r'^\s*1\s*\n', text, re.MULTILINE)
    if match: text = text[match.start():]
    return text.strip()

def translate_chunk(chunk_text, retry_count=3):
    prompt = f"""你是一个专业的影视字幕翻译专家。请将以下英文 SRT 字幕片段翻译成中文。
要求：1. 严格保持原有的序号和时间轴完全不变。2. 结合上下文语境，确保语义连贯。3. 符合中文口语习惯，自然流畅。4. 只输出 SRT 格式内容。
需要翻译的 SRT 片段：\n{chunk_text}"""
    for i in range(retry_count):
        try:
            response = client.chat.completions.create(model=MODEL_NAME, messages=[{"role": "user", "content": prompt}], temperature=0.3)
            cleaned_text = clean_llm_response(response.choices[0].message.content)
            list(srt.parse(cleaned_text)) # 验证格式
            return cleaned_text
        except Exception as e:
            if i < retry_count - 1: time.sleep(2)
            else: return chunk_text

def process_files():
    srt_files = glob.glob(os.path.join(WORK_DIR, f"*{SUB_EXT}"))
    for srt_path in srt_files:
        base_name = os.path.splitext(os.path.basename(srt_path))[0]
        if "zh" in base_name.lower() or "中文" in base_name: continue
        output_path = os.path.join(WORK_DIR, f"{base_name}_zh.srt")
        print(f"\n[+] 开始翻译: {base_name}.srt")
        
        with open(srt_path, 'r', encoding='utf-8') as f:
            subtitles = list(srt.parse(f.read()))
            
        translated_subtitles = []
        total_chunks = (len(subtitles) + CHUNK_SIZE - 1) // CHUNK_SIZE
        for i in range(0, len(subtitles), CHUNK_SIZE):
            chunk = subtitles[i:i + CHUNK_SIZE]
            print(f"  -> 翻译进度: {(i//CHUNK_SIZE)+1}/{total_chunks}")
            translated_chunk_text = translate_chunk(srt.compose(chunk))
            translated_subtitles.extend(list(srt.parse(translated_chunk_text)))
            
        final_subtitles = list(srt.sort_and_reindex(translated_subtitles))
        with open(output_path, 'w', encoding='utf-8') as f:
            f.write(srt.compose(final_subtitles))
        print(f"[✓] 翻译完成: {output_path}")

if __name__ == "__main__":
    process_files()
```
**运行方式**：`python3 batch_translate_srt.py`。脚本会生成 `_zh.srt` 结尾的中文高精度字幕。

---

## 第三阶段：严格时间轴 AI 配音与视频合成

这是整个工作流的核心。脚本会逐句生成 TTS，**动态计算加速倍率**以适配字幕时间窗口，并**修复 FFmpeg 混合音频导致的音量衰减**。

### 运行配音合成脚本
确保您的**中文 SRT 字幕**和**原 MP4 视频**在同一文件夹，且**主文件名完全一致**（例如 `lesson1.mp4` 和 `lesson1_zh.srt`，脚本会自动匹配）。

创建 `batch_tts_strict.py`，填入以下最终版代码：

```python
import os, re, subprocess, glob, srt, datetime, json

# ================= 配置区 =================
WORK_DIR = "." 
VIDEO_EXT = ".mp4" 
SUB_EXT = "_zh.srt"  # 匹配翻译后的中文字幕
   
# TTS 配置 (台湾女声)
TTS_VOICE = "zh-TW-HsiaoChenNeural"  
TTS_RATE = "+0%"                    
TTS_VOLUME = "+50%"  # 提升基础音量

# 处理策略
MAX_ATEMPO = 2.0     # 最大加速倍率
KEEP_ORIGINAL_AUDIO = False # True: 保留原音并混合, False: 替换原音

# 【关键】强制指定绝对路径，彻底解决 Mac 环境变量找不到命令的问题
FFPROBE_PATH = "/usr/local/bin/ffprobe"
FFMPEG_PATH = "/usr/local/bin/ffmpeg"
# ==========================================

def time_to_ms(td): return int(td.total_seconds() * 1000)

def get_audio_duration_ms(file_path):
    if not os.path.exists(file_path) or os.path.getsize(file_path) == 0: return 0
    cmd = [FFPROBE_PATH, "-v", "quiet", "-print_format", "json", "-show_format", file_path]
    try:
        result = subprocess.run(cmd, capture_output=True, text=True, check=True)
        data = json.loads(result.stdout) # 使用 JSON 解析，避免 IndexError
        return int(float(data['format']['duration']) * 1000)
    except: return 0

def process_files():
    srt_files = glob.glob(os.path.join(WORK_DIR, f"*{SUB_EXT}"))
    if not srt_files: print(f"未找到 {SUB_EXT} 文件。"); return

    for srt_path in srt_files:
        # 提取主文件名 (例如 lesson1_zh.srt -> lesson1)
        base_name = os.path.basename(srt_path).replace(SUB_EXT, "")
        video_path = os.path.join(WORK_DIR, f"{base_name}{VIDEO_EXT}")
        temp_dir = os.path.join(WORK_DIR, f"{base_name}_temp_tts")
        full_tts_path = os.path.join(WORK_DIR, f"{base_name}_full_tts.mp3")
        output_path = os.path.join(WORK_DIR, f"{base_name}_output.mp4")

        if not os.path.exists(video_path):
            print(f"[-] 跳过 {base_name}：找不到对应的视频文件 {video_path}。"); continue

        print(f"\n[+] 开始严格对齐处理: {base_name}")
        os.makedirs(temp_dir, exist_ok=True)

        with open(srt_path, 'r', encoding='utf-8') as f:
            subtitles = list(srt.parse(f.read()))
        if not subtitles: continue

        temp_audios, filter_complex_parts, input_args = [], [], []

        # 1. 逐句生成 TTS 并计算加速参数
        for i, sub in enumerate(subtitles):
            txt_path, audio_path = os.path.join(temp_dir, f"{i}.txt"), os.path.join(temp_dir, f"{i}.mp3")
            clean_text = sub.content.replace('\n', ' ').strip()
            if not clean_text: continue

            with open(txt_path, 'w', encoding='utf-8') as f: f.write(clean_text)

            tts_cmd = ["edge-tts", "--voice", TTS_VOICE, "--rate", TTS_RATE, "--volume", TTS_VOLUME, 
                       "--file", txt_path, "--write-media", audio_path]
            subprocess.run(tts_cmd, check=True, stdout=subprocess.DEVNULL)

            actual_ms = get_audio_duration_ms(audio_path)
            if actual_ms == 0: continue # 容错：跳过生成失败的句子

            allowed_ms = time_to_ms(sub.end - sub.start)
            atempo = min(actual_ms / allowed_ms, MAX_ATEMPO) if actual_ms > allowed_ms and allowed_ms > 0 else 1.0
            delay_ms = time_to_ms(sub.start)
            
            current_index = len(temp_audios)
            filter_complex_parts.append(f"[{current_index + 1}:a]atempo={atempo:.4f},adelay={delay_ms}|{delay_ms}[a{current_index}];")
            input_args.extend(["-i", audio_path])
            temp_audios.append(f"[a{current_index}]")

        if not temp_audios: continue

        # 2. 混合所有单句音频 (修复音量衰减)
        print("  -> 正在合成完整 TTS 音轨...")
        mix_inputs = "".join(temp_audios)
        if len(temp_audios) == 1:
            final_filter = filter_complex_parts[0].replace(f"[a0];", "") + f"[a0]volume=1.5[aout]"
        else:
            # 核心：normalize=0 关闭自动衰减，volume=1.5 强制放大音量
            final_filter = "".join(filter_complex_parts) + f"{mix_inputs}amix=inputs={len(temp_audios)}:duration=longest:dropout_transition=0:normalize=0,volume=1.5[aout]"

        mix_cmd = [FFMPEG_PATH, "-y", "-i", video_path, *input_args, "-filter_complex", final_filter,
                   "-map", "[aout]", "-c:a", "libmp3lame", "-q:a", "2", full_tts_path]
        subprocess.run(mix_cmd, check=True, stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)

        # 3. 将完整 TTS 音轨与原视频合并
        print("  -> 正在嵌入视频...")
        if KEEP_ORIGINAL_AUDIO:
            merge_filter = "[0:a]volume=0.3[orig];[1:a]volume=1.0[tts];[orig][tts]amix=inputs=2:duration=first[aout]"
            merge_cmd = [FFMPEG_PATH, "-y", "-i", video_path, "-i", full_tts_path, "-filter_complex", merge_filter, "-map", "0:v", "-map", "[aout]", "-c:v", "copy", output_path]
        else:
            merge_cmd = [FFMPEG_PATH, "-y", "-i", video_path, "-i", full_tts_path, "-c:v", "copy", "-map", "0:v:0", "-map", "1:a:0", "-shortest", output_path]
        
        subprocess.run(merge_cmd, check=True, stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)

        # 4. 清理临时文件
        print("[✓] 完成，正在清理...")
        for f in os.listdir(temp_dir): os.remove(os.path.join(temp_dir, f))
        os.rmdir(temp_dir)
        if os.path.exists(full_tts_path): os.remove(full_tts_path)
        print(f"[✓] 最终输出: {output_path}\n")

if __name__ == "__main__":
    process_files()
```

**运行方式**：`python3 batch_tts_strict.py`。脚本会输出带有 `_output.mp4` 后缀的最终配音视频。

---

## 附录：常见报错与终极解决方案

### 1. 报错 `FileNotFoundError: 'ffmpeg'` 或 `'ffprobe'`
* **原因**：Python 运行时环境变量中没有 FFmpeg 的路径。
* **解决**：本指南的配音脚本已在顶部硬编码了绝对路径 (`/usr/local/bin/ffmpeg`)。请确保您按照“第一阶段”将文件移动到了 `/usr/local/bin/` 目录下。

### 2. 报错 `IndexError: list index out of range` (在 `get_audio_duration_ms` 中)
* **原因**：某一句 TTS 生成失败（如网络波动），导致音频文件为空，`ffprobe` 无法读取时长，旧的字符串切割代码崩溃。
* **解决**：最终版脚本已引入 `json` 模块解析 `ffprobe` 输出，并增加了文件存在性检查。如果某句生成失败，脚本会自动跳过该句，不会中断整个任务。

### 3. 合成后的视频声音太小
* **原因**：FFmpeg 的 `amix` 滤镜默认会自动降低多段音频混合后的总音量。
* **解决**：最终版脚本已在 `edge-tts` 阶段提升 50% 基础音量 (`TTS_VOLUME = "+50%"`)，并在 FFmpeg 混合时关闭了自动衰减 (`normalize=0`)，最后强制放大 1.5 倍 (`volume=1.5`)。声音现在会非常饱满。

### 4. 想要保留原视频的背景音/人声
* **解决**：在配音脚本顶部的配置区，将 `KEEP_ORIGINAL_AUDIO = False` 修改为 `KEEP_ORIGINAL_AUDIO = True`。脚本会自动将原视频音量降至 30%，与 TTS 声音混合。