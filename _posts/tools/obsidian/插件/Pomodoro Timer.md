<%*
if (log.mode == "WORK") {
  if (!log.finished) {
    tR = `🟡 (pomodoro:: Unfinished) (taskName:: ${log.task.name}) (blockLinkId:: ${log.task.blockLink}) (duration:: ${log.duration}m) (begin:: ${log.begin.format("YYYY-MM-DDTHH:mm")}) - (end:: ${log.end.format("YYYY-MM-DDTHH:mm")})`;
  } else {
    tR = `🍅 (pomodoro:: WORK) (taskName:: ${log.task.name}) (blockLinkId:: ${log.task.blockLink}) (duration:: ${log.duration}m) (begin:: ${log.begin.format("YYYY-MM-DDTHH:mm")}) - (end:: ${log.end.format("YYYY-MM-DDTHH:mm")})`;
  }
} else {
  tR = `☕️ (pomodoro:: BREAK) (duration:: ${log.duration}m) (begin:: ${log.begin.format("YYYY-MM-DDTHH:mm")}) - (end:: ${log.end.format("YYYY-MM-DDTHH:mm")})`;
}
%>
