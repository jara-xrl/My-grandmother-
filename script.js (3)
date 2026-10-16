/* =========================================================
   إلى جدتي نعيمة — البرمجة التفاعلية للصفحة
========================================================= */

document.addEventListener('DOMContentLoaded', () => {

    /* ---------- شاشة التحميل ---------- */
    const loader = document.getElementById('loader');
    window.addEventListener('load', () => {
        setTimeout(() => loader.classList.add('hidden'), 600);
    });
    // fallback في حال تأخر حدث load
    setTimeout(() => loader.classList.add('hidden'), 2500);

    /* ---------- التاريخ الحالي ---------- */
    const dateOptions = { year: 'numeric', month: 'long', day: 'numeric' };
    const todayStr = new Date().toLocaleDateString('ar-EG', dateOptions);
    const dateEl = document.getElementById('currentDate');
    if (dateEl) dateEl.textContent = todayStr;
    const yearEl = document.getElementById('footerYear');
    if (yearEl) yearEl.textContent = new Date().getFullYear();

    /* ---------- شريط تقدم السكرول ---------- */
    const progressBar = document.getElementById('progressBar');
    function updateProgress() {
        const scrollTop = window.scrollY;
        const docHeight = document.documentElement.scrollHeight - window.innerHeight;
        const pct = docHeight > 0 ? (scrollTop / docHeight) * 100 : 0;
        progressBar.style.width = pct + '%';
    }
    window.addEventListener('scroll', updateProgress, { passive: true });
    updateProgress();

    /* ---------- ظهور العناصر عند التمرير ---------- */
    const revealEls = document.querySelectorAll('.reveal');
    const messageEls = document.querySelectorAll('.message');
    const revealObserver = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                entry.target.classList.add('active');
            }
        });
    }, { threshold: 0.15 });
    revealEls.forEach(el => revealObserver.observe(el));

    const messageObserver = new IntersectionObserver((entries) => {
        entries.forEach((entry, i) => {
            if (entry.isIntersecting) {
                setTimeout(() => entry.target.classList.add('visible'), i * 200);
                messageObserver.unobserve(entry.target);
            }
        });
    }, { threshold: 0.3 });
    messageEls.forEach(el => messageObserver.observe(el));

    /* ---------- القلوب المتساقطة ---------- */
    const fallingHeartsContainer = document.getElementById('fallingHearts');
    function spawnHeart(symbol) {
        const heart = document.createElement('div');
        heart.className = 'falling-heart';
        heart.textContent = symbol;
        heart.style.left = Math.random() * 100 + 'vw';
        const duration = 3 + Math.random() * 2.5;
        heart.style.animationDuration = duration + 's';
        heart.style.fontSize = (18 + Math.random() * 20) + 'px';
        heart.style.top = '-40px';
        fallingHeartsContainer.appendChild(heart);
        setTimeout(() => heart.remove(), duration * 1000 + 200);
    }

    document.querySelectorAll('.heart-btn').forEach(btn => {
        btn.addEventListener('click', () => {
            const symbol = btn.dataset.heart || '💖';
            for (let i = 0; i < 8; i++) {
                setTimeout(() => spawnHeart(symbol), i * 80);
            }
        });
    });

    // زخة قلوب خفيفة عشوائية بين الحين والآخر
    setInterval(() => {
        if (document.hidden) return;
        spawnHeart(Math.random() > 0.5 ? '💛' : '💗');
    }, 3500);

    /* ---------- خلفية الجسيمات (لمسة ذهبية هادئة) ---------- */
    const canvas = document.getElementById('particles-canvas');
    const ctx = canvas.getContext('2d');
    let particles = [];

    function resizeCanvas() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
    }
    resizeCanvas();
    window.addEventListener('resize', resizeCanvas);

    function createParticles() {
        const count = Math.min(60, Math.floor((window.innerWidth * window.innerHeight) / 22000));
        particles = Array.from({ length: count }, () => ({
            x: Math.random() * canvas.width,
            y: Math.random() * canvas.height,
            r: Math.random() * 1.8 + 0.6,
            speedY: Math.random() * 0.25 + 0.05,
            drift: Math.random() * 0.4 - 0.2,
            alpha: Math.random() * 0.5 + 0.15
        }));
    }
    createParticles();
    window.addEventListener('resize', createParticles);

    const reduceMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    function animateParticles() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        particles.forEach(p => {
            ctx.beginPath();
            ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
            ctx.fillStyle = `rgba(233, 199, 102, ${p.alpha})`;
            ctx.fill();
            p.y -= p.speedY;
            p.x += p.drift;
            if (p.y < -10) { p.y = canvas.height + 10; p.x = Math.random() * canvas.width; }
            if (p.x < -10) p.x = canvas.width + 10;
            if (p.x > canvas.width + 10) p.x = -10;
        });
        if (!reduceMotion) requestAnimationFrame(animateParticles);
    }
    animateParticles();

    /* =========================================================
       الموسيقى — لحن هادئ يُصنع مباشرة عبر Web Audio API
       (لا حاجة لملف خارجي: عزف حي يشبه القانون/العود)
    ========================================================= */
    const musicToggle = document.getElementById('musicToggle');
    let audioCtx = null;
    let musicPlaying = false;
    let musicTimer = null;
    let masterGain = null;

    // سلم خماسي (پنتاتونيك) دافئ يعطي طابعاً شرقياً هادئاً
    const scale = [261.63, 293.66, 329.63, 392.00, 440.00, 523.25, 587.33, 659.25];

    function initAudio() {
        if (audioCtx) return;
        audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        masterGain = audioCtx.createGain();
        masterGain.gain.value = 0.0001;

        // صدى خفيف عبر تأخير مع تغذية راجعة لإعطاء عمق دافئ
        const delay = audioCtx.createDelay();
        delay.delayTime.value = 0.32;
        const feedback = audioCtx.createGain();
        feedback.gain.value = 0.28;
        const delayFilter = audioCtx.createBiquadFilter();
        delayFilter.type = 'lowpass';
        delayFilter.frequency.value = 2200;

        delay.connect(feedback);
        feedback.connect(delayFilter);
        delayFilter.connect(delay);

        masterGain.connect(delay);
        delay.connect(audioCtx.destination);
        masterGain.connect(audioCtx.destination);
    }

    function pluckNote(freq, time, duration = 1.4, gainScale = 1) {
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.type = 'triangle';
        osc.frequency.value = freq;

        const osc2 = audioCtx.createOscillator();
        osc2.type = 'sine';
        osc2.frequency.value = freq * 2;
        const gain2 = audioCtx.createGain();
        gain2.gain.value = 0.15;

        gain.gain.setValueAtTime(0.0001, time);
        gain.gain.linearRampToValueAtTime(0.18 * gainScale, time + 0.03);
        gain.gain.exponentialRampToValueAtTime(0.0001, time + duration);

        osc.connect(gain);
        osc2.connect(gain2);
        gain2.connect(gain);
        gain.connect(masterGain);

        osc.start(time);
        osc.stop(time + duration + 0.05);
        osc2.start(time);
        osc2.stop(time + duration + 0.05);
    }

    function scheduleLoop() {
        if (!musicPlaying) return;
        const now = audioCtx.currentTime;
        const noteGap = 0.62;
        const patternLength = 8;

        for (let i = 0; i < patternLength; i++) {
            const note = scale[Math.floor(Math.random() * scale.length)];
            const time = now + i * noteGap;
            const emphasis = (i % 4 === 0) ? 1 : 0.6;
            pluckNote(note, time, 1.6, emphasis);
            // نغمة تناغمية خفيفة كل شوطين
            if (i % 2 === 0) {
                pluckNote(note / 2, time + 0.05, 2.0, 0.35);
            }
        }

        musicTimer = setTimeout(scheduleLoop, patternLength * noteGap * 1000);
    }

    function startMusic() {
        initAudio();
        if (audioCtx.state === 'suspended') audioCtx.resume();
        musicPlaying = true;
        masterGain.gain.cancelScheduledValues(audioCtx.currentTime);
        masterGain.gain.setValueAtTime(masterGain.gain.value, audioCtx.currentTime);
        masterGain.gain.linearRampToValueAtTime(0.9, audioCtx.currentTime + 1.2);
        scheduleLoop();
        musicToggle.classList.add('playing');
        musicToggle.setAttribute('aria-pressed', 'true');
        musicToggle.querySelector('.music-icon').textContent = '🎶';
    }

    function stopMusic() {
        musicPlaying = false;
        clearTimeout(musicTimer);
        if (masterGain && audioCtx) {
            masterGain.gain.cancelScheduledValues(audioCtx.currentTime);
            masterGain.gain.setValueAtTime(masterGain.gain.value, audioCtx.currentTime);
            masterGain.gain.linearRampToValueAtTime(0.0001, audioCtx.currentTime + 0.6);
        }
        musicToggle.classList.remove('playing');
        musicToggle.setAttribute('aria-pressed', 'false');
        musicToggle.querySelector('.music-icon').textContent = '🎵';
    }

    musicToggle.addEventListener('click', () => {
        if (musicPlaying) stopMusic();
        else startMusic();
    });

    /* =========================================================
       نافذة المشهد المتحرك — قصة قصيرة تُجسّد قيمة كل ذكرى
    ========================================================= */
    const sceneModal = document.getElementById('sceneModal');
    const sceneVisual = document.getElementById('sceneVisual');
    const sceneNarration = document.getElementById('sceneNarration');
    const sceneClose = document.getElementById('sceneClose');
    const sceneBackdrop = document.getElementById('sceneBackdrop');

    const scenes = {
        taste: {
            narration: 'كل صباح، كانت جدتي نعيمة تُعِدّ لنا المائدة بحبٍّ لا يوصف. رائحة الخبز الطازج تملأ البيت، وكأنّ كل لقمة معجونة بالحنان والدعاء.',
            build: () => {
                const wrap = document.createElement('div');
                wrap.style.position = 'relative';
                wrap.innerHTML = `
                    <div class="scene-cup">☕🍪</div>
                    <span class="steam">〜</span>
                    <span class="steam">〜</span>
                    <span class="steam">〜</span>
                `;
                return wrap;
            }
        },
        stories: {
            narration: 'في ليالٍ هادئة، كانت جدتي نعيمة تجمعنا حولها وتفتح كتاب الحكايات... صوتها الدافئ يأخذنا إلى عوالم من الخيال، حتى تغرق عيوننا في نوم هانئ.',
            build: () => {
                const wrap = document.createElement('div');
                wrap.className = 'scene-night';
                let stars = '';
                for (let i = 0; i < 10; i++) {
                    const top = Math.random() * 70;
                    const left = Math.random() * 100;
                    const delay = (Math.random() * 2).toFixed(2);
                    stars += `<span class="scene-star" style="top:${top}%; left:${left}%; animation-delay:${delay}s;">✦</span>`;
                }
                wrap.innerHTML = `
                    <div class="scene-moon">🌙</div>
                    ${stars}
                    <div class="scene-book">📖</div>
                `;
                return wrap;
            }
        },
        prayers: {
            narration: 'دعاؤكِ يا جدتي كان نوراً يرافقنا أينما ذهبنا. في كل امتحان، وكل خطوة جديدة، كانت كلماتكِ الصادقة "الله يوفقك" سرّاً نحمله في قلوبنا.',
            build: () => {
                const wrap = document.createElement('div');
                wrap.style.position = 'relative';
                let rays = '';
                for (let i = 0; i < 8; i++) {
                    const angle = i * 45;
                    rays += `<span class="ray" style="transform: translate(-50%,-100%) rotate(${angle}deg); animation-delay:${(i * 0.15).toFixed(2)}s;"></span>`;
                }
                let lights = '';
                for (let i = 0; i < 6; i++) {
                    const left = 35 + Math.random() * 30;
                    const delay = (Math.random() * 2.5).toFixed(2);
                    lights += `<span class="light-particle" style="left:${left}%; animation-delay:${delay}s;">✨</span>`;
                }
                wrap.innerHTML = `${rays}<div class="scene-hands">🤲</div>${lights}`;
                return wrap;
            }
        },
        hug: {
            narration: 'حضنكِ يا جدتي كان دائماً ملاذنا الآمن. مهما كبرنا، تبقى تلك اللحظة الدافئة أجمل مكان نلجأ إليه حين يشتد بنا الأمر.',
            build: () => {
                const wrap = document.createElement('div');
                wrap.style.position = 'relative';
                wrap.innerHTML = `
                    <span class="warmth-ring"></span>
                    <span class="warmth-ring" style="animation-delay:0.8s;"></span>
                    <span class="warmth-ring" style="animation-delay:1.6s;"></span>
                    <div class="scene-hug">🫂💞</div>
                `;
                return wrap;
            }
        }
    };

    let typingTimer = null;

    function typeNarration(text) {
        clearTimeout(typingTimer);
        sceneNarration.textContent = '';
        let i = 0;
        function step() {
            if (i <= text.length) {
                sceneNarration.textContent = text.slice(0, i);
                i++;
                typingTimer = setTimeout(step, 28);
            }
        }
        step();
    }

    function openScene(key) {
        const scene = scenes[key];
        if (!scene) return;
        sceneVisual.innerHTML = '';
        sceneVisual.appendChild(scene.build());
        typeNarration(scene.narration);
        sceneModal.classList.add('open');
        sceneModal.setAttribute('aria-hidden', 'false');
        sceneClose.focus();
        document.body.style.overflow = 'hidden';
    }

    function closeScene() {
        sceneModal.classList.remove('open');
        sceneModal.setAttribute('aria-hidden', 'true');
        document.body.style.overflow = '';
        clearTimeout(typingTimer);
    }

    document.querySelectorAll('.memory-card').forEach(card => {
        card.addEventListener('click', () => openScene(card.dataset.scene));
    });

    sceneClose.addEventListener('click', closeScene);
    sceneBackdrop.addEventListener('click', closeScene);
    document.addEventListener('keydown', (e) => {
        if (e.key === 'Escape' && sceneModal.classList.contains('open')) closeScene();
    });

});
