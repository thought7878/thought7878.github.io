【两小时，带你深入前端性能优化方案】 https://www.bilibili.com/video/BV1GAWDe7E3k/?p=14&share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862


视频讲述了在项目打包后因排除依赖包导致错误，进而介绍如何通过CDN引入解决该问题的相关内容，具体如下：

- **运行打包项目的方法**：[00:00]可通过anywhere第三方包运行，需先执行“npm i -g anywhere”安装，再*在dist目录下执行anywhere启动服务*。
- **打包后出现“xlsx is not defined”错误及原因**：[01:11]错误是因为上一小节排除了XLSX包，导致依赖包找不到。
- **解决错误需使用CDN引入**：[01:37]要利用HTMLWebpackPlugin添加CDN。
- **在vue.config.js中配置CDN**：[01:57]新增CDN对象，仅在生产环境配置，添加JS属性包含需导入的XLSX和echarts的CDN链接，再配置到HTML webpack plugin中，通过child webpack的configure注入CDN变量，触发TAP方法并返回ARGS。
- **在public/index.html中引入CDN**：[04:59]使用for循环遍历HTML webpack plugin配置中的CDN对象的JS属性，通过script标签导入对应的CDN路径。
- **验证配置效果**：[07:21]执行APM REBUILDER打包后，dist里的index.html会导入两个CDN路径，用anywhere运行项目，XLSX和echarts可正常打印，无错误。
- **留下思考问题**：[08:23]项目打包体积变小，用户访问应用时的体积是否也变小。