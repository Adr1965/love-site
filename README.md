
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <title>Barışalım mı?</title>
  <style>
    html, body {
      margin: 0; padding: 0; height: 100%; overflow: hidden;
      font-family: Arial, sans-serif; background: #f0f0f0; text-align: center;
      position: relative;
    }

    h1 {
      font-size: 4rem;
      margin-top: 20px;
      position: relative;
      z-index: 10000;
    }

    #message {
      font-size: 1.8rem;
      margin-bottom: 30px;
      position: relative;
      z-index: 10000;
    }

    #button-wrapper {
      position: absolute;
      width: 100%;
      height: 100%;
      pointer-events: none;
      user-select: none;
    }

    button {
      position: absolute;
      padding: 25px 50px;
      font-size: 2rem;
      border: none;
      border-radius: 20px;
      cursor: pointer;
      pointer-events: auto;
      user-select: none;
      white-space: nowrap;
      transition: transform 0.3s ease;
    }

    #noBtn {
      background-color: red;
      color: white;
      z-index: 11;
    }

    #yesBtn {
      background-color: green;
      color: white;
      z-index: 10;
      transform-origin: center center;
      transform: scale(1);
    }

    .full-screen-yes {
      position: fixed !important;
      top: 0 !important;
      left: 0 !important;
      width: 100vw !important;
      height: 100vh !important;
      font-size: 5rem !important;
      z-index: 9999 !important;
      border-radius: 0 !important;
      transform: scale(1) !important;
      cursor: default !important;
      display: flex !important;
      justify-content: center;
      align-items: center;
    }

    #final-message {
      font-size: 3rem;
      display: none;
      margin-top: 20px;
      position: relative;
      z-index: 10000;
      color: white;
    }

    #gif-footer {
      position: absolute;
      bottom: 0;
      width: 100%;
      max-height: 250px;
      overflow: hidden;
      z-index: 1;
    }

    #gif-container iframe {
      width: 100% !important;
      height: 250px !important;
      border: none;
      pointer-events: none;
    }
  </style>
</head>
<body>

  <h1>Barışalım mı?</h1>
  <div id="message">Kalbini dinle...</div>

  <div id="button-wrapper">
    <button id="noBtn">Tekrar Düşün</button>
    <button id="yesBtn">Evet</button>
  </div>

  <div id="final-message">Böyle diyeceğini biliyordum ❤️❤️❤️</div>

  <div id="gif-footer">
    <div id="gif-container">
      <iframe
        src="https://tenor.com/embed/10569662802514820160"
        allowfullscreen
        scrolling="no"
        frameborder="0"
      ></iframe>
    </div>
  </div>

  <script>
    const noBtn = document.getElementById("noBtn");
    const yesBtn = document.getElementById("yesBtn");
    const message = document.getElementById("message");
    const finalMessage = document.getElementById("final-message");
    const gifContainer = document.getElementById("gif-container");
    const buttonWrapper = document.getElementById("button-wrapper");

    const noMessages = [
      "Kalbini dinle...",
      "Gerçekten bu cevabı vermek istiyor musun?",
      "Senin için hâlâ çok değerliyim...",
      "Yalnız kalmak istemiyorum...",
      "Bu bir şans olabilir, kaybetme..."
    ];

    let noClickCount = 0;
    let growInterval;

    // Rastgele pozisyon üret (butonlar ekran içinde kalacak)
    function getRandomPosition(width, height) {
      const screenWidth = window.innerWidth;
      const screenHeight = window.innerHeight - 260 - 100; // Gif ve üst boşluk
      const x = Math.floor(Math.random() * (screenWidth - width));
      const y = Math.floor(Math.random() * (screenHeight - height)) + 100;
      return { x, y };
    }

    // Butonların dikey ve yatay mesafe olarak tamamen ayrılmasını sağlar
    // En az 100px aralık bırakarak üst üste binmeyi engeller
    function placeButtonsNoOverlap() {
      const noWidth = noBtn.offsetWidth;
      const noHeight = noBtn.offsetHeight;
      const yesWidth = yesBtn.offsetWidth;
      const yesHeight = yesBtn.offsetHeight;

      let noPos, yesPos;
      let attempts = 0;
      const maxAttempts = 1000;

      do {
        noPos = getRandomPosition(noWidth, noHeight);
        yesPos = getRandomPosition(yesWidth, yesHeight);

        // Kırmızı ve yeşilin dikey ve yatay mesafesini kontrol et
        const horizontalDist = Math.abs(noPos.x - yesPos.x);
        const verticalDist = Math.abs(noPos.y - yesPos.y);

        attempts++;

        // En az 100px aralık yoksa yeniden dene
        if (horizontalDist > (noWidth + yesWidth)/2 + 100 || verticalDist > (noHeight + yesHeight)/2 + 100) {
          break;
        }

      } while (attempts < maxAttempts);

      noBtn.style.left = noPos.x + "px";
      noBtn.style.top = noPos.y + "px";

      yesBtn.style.left = yesPos.x + "px";
      yesBtn.style.top = yesPos.y + "px";
    }

    noBtn.addEventListener("click", () => {
      if (noClickCount < noMessages.length) {
        message.textContent = noMessages[noClickCount];
        noBtn.textContent = noMessages[noClickCount];
      }
      noClickCount++;

      if (noClickCount < 5) {
        placeButtonsNoOverlap();

        const scale = 1 + noClickCount * 1.2;
        yesBtn.style.transform = `scale(${scale})`;
      } else if (noClickCount === 5) {
        noBtn.style.display = "none";
        message.style.display = "none";

        let scale = 1 + noClickCount * 1.2;
        yesBtn.style.position = "fixed";
        yesBtn.style.top = "50%";
        yesBtn.style.left = "50%";
        yesBtn.style.transform = `translate(-50%, -50%) scale(${scale})`;
        yesBtn.style.zIndex = 9999;
        yesBtn.style.borderRadius = "0";

        growInterval = setInterval(() => {
          scale += 0.05;
          yesBtn.style.transform = `translate(-50%, -50%) scale(${scale})`;
          if (scale >= 10) {
            clearInterval(growInterval);
            yesBtn.classList.add("full-screen-yes");
          }
        }, 20);
      }
    });

    yesBtn.addEventListener("click", () => {
      if (growInterval) clearInterval(growInterval);

      document.body.style.backgroundColor = "#28a745";

      finalMessage.style.display = "block";
      buttonWrapper.style.display = "none";
      message.style.display = "none";
      yesBtn.style.display = "none";

      gifContainer.innerHTML = `
        <iframe
          src="https://tenor.com/embed/6911161956360003031"
          allowfullscreen
          scrolling="no"
          frameborder="0"
        ></iframe>
      `;
    });

    window.onload = () => {
      placeButtonsNoOverlap();
    };

    window.onresize = () => {
      if (noBtn.style.display !== "none") {
        placeButtonsNoOverlap();
      }
    };
  </script>
</body>
</html>
