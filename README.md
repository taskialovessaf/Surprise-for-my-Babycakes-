# Surprise-for-my-Babycakes-
Made this tiny project only to make him smile ✨
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>A Little Surprise 💛</title>
  <style>
    :root{
      --bg:#0f1724; --card:#0b1220; --accent:#ff6b6b; --muted:#9aa6b2;
      --glass: rgba(255,255,255,0.04);
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }
    *{box-sizing:border-box}
    html,body{height:100%; margin:0; background:
      radial-gradient(1200px 400px at 10% 10%, rgba(255,107,107,0.06), transparent 6%),
      linear-gradient(180deg,#071023 0%, #0b1220 100%); color:#e6eef6}
    .wrap{min-height:100vh; display:flex; align-items:center; justify-content:center; padding:32px}
    .card{width:100%; max-width:900px; background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); border-radius:16px; padding:28px; box-shadow:0 8px 30px rgba(2,6,23,0.7); display:grid; grid-template-columns: 1fr 360px; gap:20px; align-items:center}
    @media (max-width:880px){ .card{grid-template-columns:1fr} .right{order:-1} }
    h1{margin:0 0 8px 0; font-size:28px; letter-spacing:-0.4px}
    p.lead{margin:0 0 16px 0; color:var(--muted)}
    .btn{display:inline-block; padding:10px 14px; border-radius:12px; background:linear-gradient(90deg,var(--accent),#ff9d87); color:#fff; text-decoration:none; font-weight:600; cursor:pointer; border:0}
    .muted{color:var(--muted); font-size:14px}
    .right{display:flex; flex-direction:column; gap:14px; align-items:center; justify-content:center}
    .photo{width:100%; max-width:320px; border-radius:12px; overflow:hidden; box-shadow:0 10px 30px rgba(0,0,0,0.5); border:1px solid rgba(255,255,255,0.03)}
    .photo img{display:block; width:100%; height:100%; object-fit:cover}
    .note{background:var(--glass); padding:14px; border-radius:12px; color:#f1f7fb}
    .actions{display:flex; gap:10px; flex-wrap:wrap}
    .small{font-size:13px; padding:8px 10px; border-radius:10px; background:transparent; border:1px solid rgba(255,255,255,0.04); cursor:pointer}
    footer{margin-top:18px; color:var(--muted); font-size:13px}
    /* simple confetti canvas */
    canvas.confetti{position:fixed; left:0; top:0; width:100%; height:100%; pointer-events:none; z-index:999}
    /* little typewriter */
    .typewriter{display:inline-block; border-right:2px solid rgba(255,255,255,0.12); padding-right:6px}
  </style>
</head>
<body>
  <canvas class="confetti" id="confettiCanvas"></canvas>

  <div class="wrap">
    <div class="card" role="main">
      <div class="left">
        <h1>Hey love, this is for you 💌</h1>
        <p class="lead">I made this silly page to make you smile. Click the buttons on the right — there's confetti, a tiny quiz and a song.</p>

        <div class="note" id="loveNote">
          <p style="margin:0">
            <span class="typewriter" id="typeText">You are my favorite hello and my hardest goodbye…</span>
          </p>
          <div style="margin-top:10px" class="muted">Tip: edit this text in the HTML to add your own private message.</div>
        </div>

        <div style="margin-top:16px" class="actions">
          <button class="btn" id="surpriseBtn">Surprise me ✨</button>
          <button class="small" id="memBtn">Memory quiz</button>
          <button class="small" id="editBtn">Edit message</button>
        </div>

        <footer>
          <div class="muted">Made with ♥ by <strong>you</strong>. Wanna change the photo? replace the <code>photo.jpg</code> file.</div>
        </footer>
      </div>

      <div class="right">
        <div class="photo">
          <!-- replace photo.jpg with your image file in same folder -->
          <img src="photo.jpg" alt="Our photo (replace photo.jpg)" id="portrait">
        </div>

        <div style="width:100%; text-align:center">
          <button class="small" id="playSongBtn">Play a song</button>
          <button class="small" id="stopSongBtn">Stop</button>
        </div>

        <div style="margin-top:8px; text-align:center">
          <button class="btn" id="confettiBtn">Celebrate 🎉</button>
        </div>
      </div>
    </div>
  </div>

  <audio id="song" src="song.mp3" preload="none"></audio>

  <script>
    // typewriter effect (simple)
    (function(){
      const textEl = document.getElementById('typeText');
      const full = textEl.textContent;
      textEl.textContent = '';
      let i=0;
      function step(){
        if(i<=full.length){
          textEl.textContent = full.slice(0,i++);
          setTimeout(step, 40);
        } else {
          textEl.style.borderRight = 'none';
        }
      }
      step();
    })();

    // tiny memory quiz
    document.getElementById('memBtn').addEventListener('click', ()=>{
      const q = prompt("When did we first meet? (type the date or month)");
      if(!q) return alert("Aw, no answer 💔");
      if(q.toLowerCase().includes("march") || q.toLowerCase().includes("mar") ) {
        alert("Correct-ish! You remembered the season of my heart 😊");
      } else {
        alert("Close enough — love doesn't keep score. 💛");
      }
    });

    // edit message (client-side only)
    document.getElementById('editBtn').addEventListener('click', ()=>{
      const newMsg = prompt("Edit the message that appears on the page:", document.getElementById('typeText').textContent);
      if(newMsg !== null) {
        document.getElementById('typeText').textContent = newMsg;
        // remove cursor after edit
        setTimeout(()=> document.getElementById('typeText').style.borderRight='none', 3000);
      }
    });

    // audio controls
    const song = document.getElementById('song');
    document.getElementById('playSongBtn').addEventListener('click', ()=> {
      song.play().catch(()=> alert("Add a file called song.mp3 in the same folder to play."));
    });
    document.getElementById('stopSongBtn').addEventListener('click', ()=> song.pause());

    // confetti - simple particles
    (function(){
      const canvas = document.getElementById('confettiCanvas');
      const ctx = canvas.getContext('2d');
      let W, H, particles = [], running=false;

      function resize(){ W = canvas.width = innerWidth; H = canvas.height = innerHeight; }
      window.addEventListener('resize', resize);
      resize();

      function spawn(x,y){
        for(let i=0;i<90;i++){
          particles.push({
            x: x || W/2, y: y || H/3,
            vx: (Math.random()-0.5)*8, vy: Math.random()*6-2,
            size: 6+Math.random()*8,
            color: ['#ff6b6b','#ffd166','#06d6a0','#8ecae6'][Math.floor(Math.random()*4)],
            rot: Math.random()*360, vr: (Math.random()-0.5)*10, life: 120+Math.random()*80
          });
        }
        if(!running){ running=true; requestAnimationFrame(loop); }
      }

      function loop(){
        ctx.clearRect(0,0,W,H);
        for(let i=particles.length-1;i>=0;i--){
          const p = particles[i];
          p.x += p.vx; p.y += p.vy; p.vy += 0.18; p.rot += p.vr; p.life--;
          ctx.save();
            ctx.translate(p.x,p.y);
            ctx.rotate(p.rot*Math.PI/180);
            ctx.fillStyle = p.color;
            ctx.fillRect(-p.size/2, -p.size/2, p.size, p.size*0.6);
          ctx.restore();
          if(p.y>H+50 || p.life<=0) particles.splice(i,1);
        }
        if(particles.length) requestAnimationFrame(loop);
        else running=false;
      }

      window.launchConfetti = spawn;
    })();

    // Buttons
    document.getElementById('confettiBtn').addEventListener('click', ()=> window.launchConfetti());
    document.getElementById('surpriseBtn').addEventListener('click', ()=> {
      window.launchConfetti();
      setTimeout(()=> alert("Surprise! 💛 I hope that made you smile."), 400);
    });

    // small improvement: clicking the photo rotates it for a second
    document.getElementById('portrait').addEventListener('click', function(){
      this.style.transition = 'transform 0.6s';
      this.style.transform = 'rotate(-6deg) scale(1.02)';
      setTimeout(()=>{ this.style.transform=''; }, 800);
    });

  </script>
</body>
</html>
