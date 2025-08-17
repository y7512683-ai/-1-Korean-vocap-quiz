<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>Word Quiz</title>
  <meta name="viewport" content="width=900">
  <style>
    body {
      background: linear-gradient(135deg, #f8fbff 0%, #e2eeff 100%);
      font-family: 'Gmarket Sans', 'Segoe UI', 'Malgun Gothic', Arial, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
      margin: 0;
    }
    .ppt-container {
      background: #fffdfb;
      box-shadow: 0 6px 48px 0 #cad7ef86;
      border-radius: 50px;
      margin-top: 60px;
      width: 850px;
      min-height: 540px;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 80px 0 60px 0;
      position: relative;
      border: 4px dashed #ffb0c8;
      animation: popin 0.7s cubic-bezier(.68,-0.55,.27,1.55);
    }
    @keyframes popin {
      0% { transform: scale(0.93); opacity:0.3;}
      90% { transform: scale(1.03);}
      100% { transform: scale(1); opacity:1;}
    }
    .ppt-question {
      font-size: 5.5rem;
      font-weight: 800;
      color: #ff66a6;
      margin-bottom: 40px;
      cursor: pointer;
      user-select: none;
      transition: color 0.2s, text-shadow 0.2s;
      text-align: center;
      text-shadow: 0 4px 24px #ffe3f1;
      letter-spacing: 3px;
    }
    .ppt-question:hover {
      color: #ffb0c8;
      text-shadow: 0 8px 32px #ffecf6;
    }
    .ppt-answer {
      font-size: 4rem;
      color: #3079ed;
      font-weight: 700;
      margin-top: 48px;
      min-height: 80px;
      opacity: 0;
      transition: opacity 0.3s;
      text-align: center;
      letter-spacing: 3px;
      background: #f5f9ff;
      border-radius: 35px;
      padding: 28px 0;
      box-shadow: 0 4px 24px #c3d6ed4c;
      border: 3px solid #b8e0ff;
      position: relative;
      z-index: 1;
    }
    .ppt-answer.show {
      opacity: 1;
      animation: popin 0.6s cubic-bezier(.68,-0.55,.27,1.55);
    }
    .ppt-emoji {
      font-size: 4.5rem;
      margin-left: 18px;
      vertical-align: middle;
      filter: drop-shadow(0 6px 24px #e2e7f5);
      transition: transform 0.3s;
      animation: helloemoji 0.7s cubic-bezier(.68,-0.55,.27,1.55);
    }
    @keyframes helloemoji {
      0% { transform: scale(0.7) rotate(-10deg);}
      70% { transform: scale(1.3) rotate(10deg);}
      100% { transform: scale(1) rotate(0);}
    }
    .ppt-controls {
      display: flex;
      gap: 44px;
      margin-top: 88px;
    }
    .ppt-btn {
      padding: 22px 78px;
      background: linear-gradient(90deg, #ffb0c8 70%, #ffd5c0 100%);
      color: white;
      font-size: 2.5rem;
      border: none;
      border-radius: 33px;
      cursor: pointer;
      font-weight: 800;
      box-shadow: 0 4px 24px #ffe3f1;
      transition: background 0.2s, transform 0.15s;
      letter-spacing: 2px;
    }
    .ppt-btn:active {
      transform: scale(0.97);
    }
    .ppt-btn:disabled {
      background: #e9e9e9;
      color: #b0b3c1;
      cursor: not-allowed;
      box-shadow: none;
    }
    .ppt-count {
      position: absolute;
      top: 38px;
      right: 60px;
      font-size: 1.3rem; /* 작게 변경 */
      color: #8092b6;
      font-weight: 700;
      background: #f5f9ff;
      border-radius: 18px;
      padding: 6px 18px;
      box-shadow: 0 2px 8px #c3d6ed4c;
    }
    @media (max-width: 1000px) {
      .ppt-container {
        width: 99vw;
        min-height: 340px;
        padding: 28px 0;
      }
      .ppt-question {
        font-size: 3.2rem;
      }
      .ppt-answer {
        font-size: 2.7rem;
        min-height: 45px;
        padding: 18px 0;
      }
      .ppt-emoji {
        font-size: 2.5rem;
      }
      .ppt-btn {
        font-size: 1.3rem;
        padding: 13px 40px;
      }
      .ppt-count {
        font-size: 1rem;
        padding: 4px 10px;
        top: 16px;
        right: 10px;
      }
    }
  </style>
</head>
<body>
  <div class="ppt-container">
    <div class="ppt-count" id="slide-count"></div>
    <div class="ppt-question" id="ppt-question" onclick="showAnswer()"></div>
    <div class="ppt-answer" id="ppt-answer"></div>
    <div class="ppt-controls">
      <button class="ppt-btn" id="prev-btn" onclick="prevSlide()">이전</button>
      <button class="ppt-btn" id="next-btn" onclick="nextSlide()">다음</button>
    </div>
  </div>
<script>
const quiz = [
  { ko: "이름", en: "Name", emoji: "✔️" },         // 변경됨
  { ko: "직업", en: "Job / Occupation", emoji: "💼" },
  { ko: "학생", en: "Student", emoji: "👩‍🎓" },
  { ko: "회사원", en: "Office worker", emoji: "🏢" },
  { ko: "의사", en: "Doctor", emoji: "🩺" },
  { ko: "가수", en: "Singer", emoji: "🎤" },
  { ko: "요리사", en: "Cook / Chef", emoji: "👨‍🍳" },
  { ko: "선생님", en: "Teacher", emoji: "👩‍🏫" },
  { ko: "경찰", en: "Police officer", emoji: "👮‍♂️" },
  { ko: "대학생", en: "College student", emoji: "🎓" },
  { ko: "동생", en: "Younger sibling", emoji: "🧑" }, // 변경됨
  { ko: "아버지 (아빠)", en: "Father", emoji: "👨" },
  { ko: "어머니 (엄마)", en: "Mother", emoji: "👩" },
  { ko: "언니", en: "Older sister (female)", emoji: "👧" },
  { ko: "형", en: "Older brother (male)", emoji: "👦" },
  { ko: "친구", en: "Friend", emoji: "🤝" },
  { ko: "누구", en: "Who", emoji: "❓" },
  { ko: "나라", en: "Country", emoji: "🌏" },
  { ko: "한국 사람", en: "Korean person", emoji: "🇰🇷" },
  { ko: "캐나다 사람", en: "Canadian person", emoji: "🇨🇦" },
  { ko: "미국 사람", en: "American person", emoji: "🇺🇸" },
  { ko: "중국 사람", en: "Chinese person", emoji: "🇨🇳" },
  { ko: "일본 사람", en: "Japanese person", emoji: "🇯🇵" },
  { ko: "모자", en: "Hat", emoji: "🧢" },
  { ko: "책", en: "Book", emoji: "📖" },
  { ko: "공책", en: "Notebook", emoji: "📒" }
];

let idx = 0;
let modeKo = true; // true면 한국어 문제, false면 영어+이모지 정답

function renderSlide() {
  document.getElementById('slide-count').textContent = `${idx+1} / ${quiz.length}`;
  document.getElementById('ppt-answer').classList.remove('show');

  // 한국어 문제 상태
  if (modeKo) {
    document.getElementById('ppt-question').textContent = quiz[idx].ko;
    document.getElementById('ppt-answer').textContent = "";
  }
  // 영어+이모지 정답 상태
  else {
    document.getElementById('ppt-question').textContent = "";
    document.getElementById('ppt-answer').innerHTML =
      `<span style="color:#3079ed">${quiz[idx].en}</span> <span class="ppt-emoji">${quiz[idx].emoji}</span>`;
    document.getElementById('ppt-answer').classList.add('show');
  }

  document.getElementById('prev-btn').disabled = idx === 0 && modeKo;
  document.getElementById('next-btn').disabled = idx === quiz.length - 1 && !modeKo;
}

function showAnswer() {
  // 한국어 문제에서 정답 보여주기 → 영어+이모지
  if (modeKo) {
    modeKo = false;
    renderSlide();
  } else {
    nextSlide();
  }
}

function nextSlide() {
  // 다음 문제로 이동
  if (idx < quiz.length - 1) {
    idx++;
    modeKo = true;
    renderSlide();
  }
}

function prevSlide() {
  if (idx > 0) {
    idx--;
    modeKo = true;
    renderSlide();
  }
}

renderSlide();
</script>
</body>
</html>
