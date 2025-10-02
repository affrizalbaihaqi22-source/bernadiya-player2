<!doctype html>
<html lang="id">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Player — Bernadya "Untungnya"</title>
  <style>
    :root{--bg:#0f1724;--card:#0b1220;--accent:#06b6d4;--muted:#94a3b8}
    *{box-sizing:border-box}
    body{font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, 'Helvetica Neue', Arial; margin:0; min-height:100vh; display:flex; align-items:center; justify-content:center; background:linear-gradient(180deg,#081229 0%, #071228 100%); color:#e6eef6}
    .player{width:360px; background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.05)); border-radius:16px; padding:20px; box-shadow:0 8px 30px rgba(2,6,23,0.6)}
    .cover{width:100%; height:180px; border-radius:12px; background:linear-gradient(135deg,var(--accent),#7c3aed); display:flex; align-items:center; justify-content:center; font-weight:700; font-size:20px}
    .meta{margin-top:12px}
    .title{font-size:18px; font-weight:700}
    .artist{color:var(--muted); font-size:13px; margin-top:4px}
    .controls{display:flex; align-items:center; gap:12px; margin-top:14px}
    .big-btn{width:56px; height:56px; border-radius:12px; display:grid; place-items:center; background:rgba(255,255,255,0.03); cursor:pointer; border:1px solid rgba(255,255,255,0.03)}
    .play-icon{width:0;height:0;border-left:14px solid #e6eef6;border-top:10px solid transparent;border-bottom:10px solid transparent}
    .pause-icon{width:16px; height:16px; display:flex; gap:6px}
    .pause-icon>div{width:4px; background:#e6eef6; border-radius:2px}
    .small-btn{padding:8px 10px; border-radius:10px; background:transparent; border:1px solid rgba(255,255,255,0.03); cursor:pointer}
    .progress{width:100%; height:8px; background:rgba(255,255,255,0.03); border-radius:6px; overflow:hidden; margin-top:12px}
    .progress > .bar{height:100%; width:0%; background:linear-gradient(90deg,var(--accent), #7c3aed)}
    .time-row{display:flex; justify-content:space-between; color:var(--muted); font-size:12px; margin-top:6px}
    .small{font-size:12px; color:var(--muted); margin-top:10px}
  </style>
</head>
<body>
  <div class="player" role="region" aria-label="Audio player">
    <div class="cover" id="cover">Bernadya — Untungnya</div>
    <div class="meta">
      <div class="title">Untungnya</div>
      <div class="artist">Bernadya</div>

      <div class="controls">
        <button class="big-btn" id="playBtn" aria-label="Play/Pause">
          <span id="playIcon" class="play-icon"></span>
        </button>
        <div style="flex:1">
          <div class="progress" id="progress" role="progressbar" aria-valuemin="0" aria-valuemax="100">
            <div class="bar" id="bar"></div>
          </div>
          <div class="time-row"><span id="curTime">0:00</span><span id="dur">0:00</span></div>
        </div>
        <div class="vol">
          <button class="small-btn" id="rewind" title="Rewind 5s">⏪</button>
          <button class="small-btn" id="forward" title="Forward 5s">⏩</button>
        </div>
      </div>

      <div class="small" style="margin-top:8px">File MP3 sudah dimuat otomatis dan akan diputar saat halaman dibuka.</div>

    </div>
  </div>

  <audio id="audio" preload="metadata" autoplay src="Bernadya - Untungnya, Hidup Harus Tetap Berjalan (Official Video) [HB8vftGxsIc].mp3"></audio>

  <script>
    const audio = document.getElementById('audio');
    const playBtn = document.getElementById('playBtn');
    const playIcon = document.getElementById('playIcon');
    const bar = document.getElementById('bar');
    const progress = document.getElementById('progress');
    const curTime = document.getElementById('curTime');
    const durTime = document.getElementById('dur');
    const rewind = document.getElementById('rewind');
    const forward = document.getElementById('forward');

    function fmt(t){
      if (!isFinite(t)) return '0:00';
      t = Math.floor(t);
      const m = Math.floor(t/60);
      const s = t%60;
      return m + ':' + String(s).padStart(2,'0');
    }

    function updatePlayIcon(){
      if(audio.paused){
        playIcon.className = 'play-icon';
        playBtn.setAttribute('aria-pressed','false');
      }else{
        playIcon.className = 'pause-icon';
        playBtn.setAttribute('aria-pressed','true');
      }
    }

    playBtn.addEventListener('click', ()=>{
      if(audio.paused) audio.play(); else audio.pause();
    });

    audio.addEventListener('play', updatePlayIcon);
    audio.addEventListener('pause', updatePlayIcon);

    audio.addEventListener('timeupdate', ()=>{
      if(audio.duration){
        const pct = (audio.currentTime / audio.duration) * 100;
        bar.style.width = pct + '%';
        curTime.textContent = fmt(audio.currentTime);
        durTime.textContent = fmt(audio.duration);
      }
    });

    audio.addEventListener('loadedmetadata', ()=>{
      durTime.textContent = fmt(audio.duration);
    });

    progress.addEventListener('click', (e)=>{
      if(!audio.duration) return;
      const rect = progress.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const pct = x / rect.width;
      audio.currentTime = pct * audio.duration;
    });

    rewind.addEventListener('click', ()=>{ audio.currentTime = Math.max(0, audio.currentTime - 5); });
    forward.addEventListener('click', ()=>{ if(audio.duration) audio.currentTime = Math.min(audio.duration, audio.currentTime + 5); });

    window.addEventListener('keydown', (e)=>{
      if(e.code === 'Space') { e.preventDefault(); audio.paused ? audio.play() : audio.pause(); }
      if(e.code === 'ArrowLeft') { audio.currentTime = Math.max(0, audio.currentTime - 5); }
      if(e.code === 'ArrowRight') { if(audio.duration) audio.currentTime = Math.min(audio.duration, audio.currentTime + 5); }
    });

    audio.addEventListener('ended', ()=>{ playBtn.focus(); updatePlayIcon(); });
    updatePlayIcon();
  </script>
</body>
</html>
