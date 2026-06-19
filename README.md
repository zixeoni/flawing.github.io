<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flawing - wingstall Archiving</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { background-color: #0a0a0a; color: #ffffff; font-family: 'Noto Sans KR', sans-serif; overflow-y: auto; }
        
        .intro-section { display: grid; grid-template-columns: 1fr 1fr; gap: 40px; padding: 60px 40px; cursor: pointer; border-bottom: 1px solid #222; }
        .intro-img { width: 100%; aspect-ratio: 16/9; background: #222; object-fit: cover; }
        .intro-text { display: flex; flex-direction: column; justify-content: center; }
        .intro-text h2 { font-size: 3rem; margin-bottom: 20px; }

        .gallery-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 15px; padding: 40px; }
        .gallery-item { position: relative; padding-top: 100%; background: #111; cursor: pointer; overflow: hidden; }
        .gallery-item img, .gallery-item video { position: absolute; top: 0; left: 0; width: 100%; height: 100%; object-fit: cover; transition: transform 0.3s; }
        .gallery-item:hover img, .gallery-item:hover video { transform: scale(1.05); }

        /* Contact 섹션 스타일 */
        .contact-grid { display: grid; grid-template-columns: repeat(5, 1fr); gap: 20px; padding: 40px; }
        .contact-item { text-align: center; background: #111; padding: 20px; border-radius: 8px; }
        .contact-item img { width: 80px; height: 80px; border-radius: 50%; object-fit: cover; margin-bottom: 10px; background: #222; }
        .contact-item h4 { margin-bottom: 5px; }
        .contact-item p { color: #888; font-size: 0.9rem; }

        /* 모달 스타일 유지 */
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 1000; justify-content: center; align-items: center; cursor: pointer; visibility: hidden; opacity: 0; transition: opacity 0.3s ease-in-out, visibility 0.3s ease-in-out; }
        .modal.show { display: flex; visibility: visible; opacity: 1; }
        .modal-media { max-width: 80vw; max-height: 80vh; object-fit: contain; cursor: default; }
        .modal-text { width: 85vw; max-width: 1100px; max-height: 85vh; overflow-y: auto; background: rgba(30, 30, 30, 0.7); backdrop-filter: blur(15px); padding: 60px; cursor: default; border-radius: 15px; line-height: 1.8; }
        .close-btn { position: fixed; top: 30px; right: 40px; font-size: 40px; cursor: pointer; color: #fff; z-index: 1001; }
    </style>
</head>
<body>

    <section class="intro-section" onclick="openTextModal()">
        <img src="wing1.jpg" class="intro-img" alt="대표 이미지">
        <div class="intro-text">
            <span style="color:#666;">wingstall</span>
            <h2>Flawing</h2>
            <p>이태하, 김형록, 박나연, 육지헌, 최준용</p>
        </div>
    </section>

    <main class="gallery-grid" id="gallery"></main>

    <h2 style="padding: 40px 40px 0; font-size: 1.5rem;">Contact</h2>
    <div class="contact-grid" id="contact"></div>

    <div class="modal" id="modal">
        <span class="close-btn">&times;</span>
        <div id="modalBody"></div>
    </div>

    <script>
        // 1. 데이터 및 갤러리 로직
        const galleryItems = ['wing11.JPG','wing12.JPG','Wing13.mp4','wing14.JPG','Wing15.mp4','Wing16.mp4','wing17.JPG','Wing18.mp4','Wing19.mp4','wing110.JPG','wing111.JPG','wing112.JPG','Wing113.mp4','Wing114.mp4','Wing115.mp4','Wing116.mp4','Wing118.mp4','wing119.JPG','wing120.mp4','wing301.jpeg','wing302.jpeg','wing303.jpeg','wing304.jpeg','Wing30.mp4','wing31.JPG','wing32.JPG','wing33.JPG','Wing33-2.mp4','wing35.JPG','Wing37.mp4','wing38.JPG','wing39.JPG','wing310.jpeg','Wing41.mp4', 'Wing42.mp4','Wing43.mp4','Wing44.mp4','Wing45.mp4','Wing46.mp4','Wing47.mp4','Wing48.mp4','Wing49.mp4','Wing410.mp4','Wing51.mp4','wing52.jpeg','wing52-1.jpeg','wing53.jpeg'];
        
        const introDetail = { title: "Flawing", desc: "<br>이 작품에서 날개의 의미는 단순히 비행만을 위한 기관이 아닌 <삶을 유지하게 하는 무언가>에 의해 죽음에 저항하는 존재이며 그것을 보는 사람에게 각자의 삶의 이유를 떠올리게 하는 장치로서의 역할을 한다. 완전보다 불완전에 공감하는 인간의 특성을 파악하고 완전한 물체가 결함을 가졌을 때 그것의 불완전함이 강조되는 효과를 날개의 외형적 결함과 움직임의 불연속성을 통해 표현하여 작품을 보는 사람들의 공감을 이끌어내 각자의 모습을 떠올리게 하며 불완전함에도 움직임을 반복하는 모습과 그 움직임으로 만들어지는 그림자를 통해 관람객이 날개에 자신의 모습을 투영할 수 있는 시간을 제공한다", intent: "<br>중력에 저항하며 삶을 유지하게 하는 날개의 날갯짓은 죽음에 저항하며 삶을 유지하는 인간의 삶을 떠올리게 한다. 우리는 이러한 날개의 움직임과 못브을 통해 공감할 수 있는 감각을 만들고 각자의 모습을 투영하여 스스로에게 삶의 이유가 무엇인지에 대한 질문을 던지게 한다." };
        const teamMembers = [{ name: "이태하", role: "Engineering", contact: "email@example.com", img: "p1.jpeg" }, { name: "김형록", role: "Motor Control", contact: "email@example.com", img: "p2.jpeg" }, { name: "박나연", role: "Project Manager", contact: "email@example.com", img: "p3.jpeg" }, { name: "육지헌", role: "Fabrication", contact: "email@example.com", img: "p4.jpeg" }, { name: "최준용", role: "Frame Design", contact: "email@example.com", img: "p5.jpeg" }];

        function isVideo(src) { return src.endsWith('.mp4') || src.endsWith('.webm') || src.endsWith('.ogg'); }

        const galleryContainer = document.getElementById('gallery');
        galleryItems.forEach(src => {
            const div = document.createElement('div');
            div.className = 'gallery-item';
            div.innerHTML = isVideo(src) ? `<video src="${src}" muted playsinline loop autoplay></video>` : `<img src="${src}" alt="Gallery Item">`;
            div.onclick = () => openMediaModal(src);
            galleryContainer.appendChild(div);
        });

        // 2. Contact 로직 (div가 생성된 후 실행)
        const contactContainer = document.getElementById('contact');
        teamMembers.forEach(member => {
            const div = document.createElement('div');
            div.className = 'contact-item';
            div.innerHTML = `<img src="${member.img}" alt="${member.name}"><h4>${member.name}</h4><p>${member.role}</p><p style="font-size: 0.8rem; color: #555;">${member.contact}</p>`;
            contactContainer.appendChild(div);
        });

        // 3. 모달 관련 함수들
        function openTextModal() {
            document.getElementById('modalBody').className = 'modal-text';
            document.getElementById('modalBody').innerHTML = `<div style="display: flex; gap: 40px; align-items: start;"><div style="flex: 0 0 35%;"><img src="poster.png" style="width: 100%; border-radius: 4px;"></div><div style="flex: 1;"><h1 style="margin-bottom: 20px;">${introDetail.title}</h1><p style="margin-bottom: 25px;"><strong>작품설명</strong>${introDetail.desc}</p><p><strong>기획의도</strong>${introDetail.intent}</p></div></div>`;
            document.getElementById('modal').classList.add('show');
        }

        function openMediaModal(src) {
            document.getElementById('modalBody').className = 'modal-media';
            document.getElementById('modalBody').innerHTML = isVideo(src) ? `<video src="${src}" class="modal-media" controls autoplay muted></video>` : `<img src="${src}" class="modal-media">`;
            document.getElementById('modal').classList.add('show');
        }

        function closeModal() {
            document.getElementById('modal').classList.remove('show');
            setTimeout(() => { document.getElementById('modalBody').innerHTML = ''; }, 300);
        }

        document.getElementById('modal').addEventListener('click', function(e) { if (e.target.id === 'modal') closeModal(); });
        document.querySelector('.close-btn').addEventListener('click', closeModal);
    </script>
</body>
</html>
