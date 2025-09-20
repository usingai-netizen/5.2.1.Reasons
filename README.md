<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>工業革命起源探索之旅（多語版）</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    :root{
      --gold:#f39c12;
      --ink:#111827;
    }
    body{
      background: linear-gradient(180deg, #ffffff, #f6f8fb 70%);
      color: var(--ink);
      font-family: "Noto Sans TC", "PingFang TC", "Microsoft JhengHei", "Heiti TC", system-ui, -apple-system, "Segoe UI", Arial, sans-serif;
      letter-spacing:.2px;
    }
    .surface{ background:#fff; border:1px solid #e5e7eb; box-shadow:0 6px 18px rgba(17,24,39,.06); border-radius:1rem; }
    .surface-soft{ background:#fbfdff; border:1px solid #e5e7eb; border-radius:1rem; }
    .btn{ display:inline-flex; align-items:center; justify-content:center; gap:.5rem; padding:.7rem 1rem; border-radius:.7rem; font-weight:700; letter-spacing:.2px; transition:all .2s ease; }
    .btn-primary{ background:linear-gradient(180deg, #f7b844, var(--gold)); color:#1c2430; box-shadow:0 6px 14px rgba(243,156,18,.25), inset 0 1px 0 rgba(255,255,255,.5); }
    .btn-primary:hover{ filter:brightness(1.06); transform:translateY(-1px);}
    .btn-secondary{ background:#eef2f7; color:#1f2a37; border:1px solid #d5dde6; }
    .btn-secondary:hover{ background:#e6ecf4; }
    .progress-wrap{ height:10px; background:#eef2f7; border-radius:999px; overflow:hidden; border:1px solid #e2e8f0; }
    .progress-bar{ height:100%; background:linear-gradient(90deg, #f7b844, var(--gold)); transition:width .35s ease; }
    .badge-spot{ width:64px; height:64px; border-radius:14px; background:#f8fafc; border:1px solid #e2e8f0; display:flex; align-items:center; justify-content:center; font-size:28px; transition:transform .2s, box-shadow .2s, border-color .2s; }
    .badge-spot.active{ background:#fff8e8; border-color:#f1c27a; box-shadow:0 8px 20px rgba(243,156,18,.2), inset 0 0 0 1px rgba(243,156,18,.5); }
    .pop-in { animation: pop-in .5s cubic-bezier(.2,1.2,.2,1); }
    @keyframes pop-in{ 0%{ transform:scale(.6); opacity:0;} 60%{ transform:scale(1.08); opacity:1;} 100%{ transform:scale(1);} }
    .toast{ animation:toast-in .4s ease, toast-out .3s ease 2.9s forwards; }
    @keyframes toast-in{ from{ transform:translateY(-8px); opacity:0;} to{ transform:translateY(0); opacity:1;} }
    @keyframes toast-out{ to{ transform:translateY(-8px); opacity:0;} }
    .option{ border:1px solid #e2e8f0; border-radius:.8rem; padding:.8rem .9rem; background:#fff; transition:border-color .2s, box-shadow .2s; }
    .option:hover{ border-color:#cbd5e1; box-shadow:0 2px 8px rgba(15,23,42,.06); }
    .option input{ accent-color:var(--gold); }
    .btn:focus, button:focus, input:focus, textarea:focus{ outline:2px solid var(--gold); outline-offset:2px; }
    .muted{ color:#475569; }
    .lang-btn{ padding:.25rem .5rem; font-weight:700; font-size:.85rem; }
    .lang-active{ background:#1f2937; color:#fff; }
    .lang-inactive{ background:#f3f4f6; color:#1f2937; }
  </style>
</head>
<body>
  <!-- Header -->
  <header class="w-full bg-white/95 backdrop-blur sticky top-0 z-20 border-b border-slate-200">
    <div class="max-w-6xl mx-auto px-4 py-3 flex items-center gap-4">
      <!-- Language toggle (left-most) -->
      <div class="inline-flex border border-slate-300 rounded-lg overflow-hidden">
        <button id="langZh" class="lang-btn">中</button>
        <button id="langEn" class="lang-btn">ENG</button>
      </div>

      <div class="flex items-center gap-3">
        <div class="w-10 h-10 rounded-xl flex items-center justify-center bg-[#f3f6fb] border border-[#e2e8f0]">
          <span class="text-2xl">🔧</span>
        </div>
        <div>
          <div id="appTitle" class="text-lg font-extrabold">工業革命起源探索之旅</div>
          <div id="appSubtitle" class="text-xs text-slate-600">遊戲式歷史任務 · 以英國為何率先起步為線索</div>
        </div>
      </div>
      <div class="flex-1"></div>
      <nav id="chapterDots" class="hidden md:flex items-center gap-4 pr-2"></nav>
    </div>
    <div class="max-w-6xl mx-auto px-4 pb-4">
      <div class="progress-wrap"><div id="progressBar" class="progress-bar" style="width:0%"></div></div>
      <div id="progressLabel" class="text-xs text-slate-600 mt-1">進度：0%</div>
    </div>
  </header>

  <!-- Toast -->
  <div id="toastArea" class="fixed left-1/2 -translate-x-1/2 top-4 z-30 space-y-2"></div>

  <!-- Main -->
  <main class="max-w-6xl mx-auto px-4 py-6">
    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">
      <section id="content" class="lg:col-span-8 surface p-4 md:p-6"></section>
      <aside class="lg:col-span-4">
        <div class="surface p-3 md:p-4 rounded-2xl sticky top-24">
          <div class="flex items-center justify-between mb-2">
            <div id="achTitle" class="font-bold text-sm">條件成就</div>
            <div class="text-yellow-500 text-xl">🏅</div>
          </div>
          <div class="grid grid-cols-3 gap-3" id="achievements"></div>
          <div id="achNote" class="text-[12px] text-slate-600 mt-2">集齊六項條件，解鎖最終總結</div>
        </div>
      </aside>
    </div>
  </main>

  <script>
    // ====== I18N ======
    const ui = {
      zh: {
        appTitle: '工業革命起源探索之旅',
        appSubtitle: '遊戲式歷史任務 · 以英國為何率先起步為線索',
        progress: '進度',
        start: '開始探索',
        backCover: '返回封面',
        missionTitle: '任務說明',
        missionIntro: '今天我們將通過解答問題，逐步解鎖三個關鍵人物的故事：托馬斯、瑪麗、威廉。每答對一題，你就能推進劇情，收集成就徽章。集齊六大條件：原料、勞動力、資金、市場、分工協作、科技，便能整合出「為何英國率先展開工業革命」的答案。',
        enterCh1: '進入第一章（托馬斯）',
        thinkQ: '思考提問：如果沒有機器，你的生活會怎樣？',
        achievements: '條件成就',
        achievementsNote: '集齊六項條件，解鎖最終總結',
        badgeLabels: { '原料':'原料', '募動力?':'', '勞動力':'勞動力', '資金':'資金', '市場':'市場', '分工':'分工', '科技':'科技' },
        statusDone: '已完成',
        statusProgress: '進行中',
        statusLocked: '🔒 未解鎖',
        storyTags: ['（勞動力、原料）','（分工協作）','（資金、市場、科技、資源）'],
        btnContinue: '繼續',
        btnConfirm: '確認',
        btnHome: '返回主頁',
        btnBackStep: '返回上一步',
        btnSubmitView: '提交並查看參考',
        savedThought: '🗒️ 已記錄你的想法，繼續前進！',
        toastUnlocked: '🔓 下一章已解鎖！',
        toastLocked: '🔒 尚未解鎖',
        toastAch: (name)=>`✔️ 成就達成：${name}`,
        qDifficulty: '難度',
        qPrompt: '提問',
        openShare: '分享我的想法',
        coverTitle: '工業革命之謎：為何從英國開始？',
        missionCard: ['原料','勞動力','資金','市場','分工','科技'],
        summaryControlsNote: '完成所有步驟即可通關',
        summary1Title: '六大條件，解鎖成功！',
        summary1Text: `
📌 思維導圖式總結（中心：英國）
- 🐑 原料：羊毛／棉花 → 托馬斯（圈地→羊毛）、威廉（海外棉花）
- 👥 勞動力：人口向城市集中 → 托馬斯（進城找工）
- 💰 資金：農業改良與商業利潤 → 威廉（投資工廠與機器）
- 🚢 市場：海港與全球商路 → 威廉（海外市場拓展）、瑪麗（價格下降帶動需求）
- 🧩 分工：工場制度拆分工序 → 瑪麗（工序協作）
- ⚙️ 科技：機械與改良 → 瑪麗（珍妮機）、威廉（學會與專利、蒸汽機）`.trim(),
        quizTitle: (n)=>`鞏固遊戲｜問題 ${n}`,
        tfLabel: ['對','錯'],
        openTitle: (n)=>`鞏固遊戲｜問題 ${n}`,
        refAnswer: '參考答案',
        summaryEndTitle: '結語',
        summaryEndText: '正是這六個條件的完美結合，讓工業革命這顆種子只有在英國的土壤中才能最先發芽。🎉 恭喜通關！',
        btnRestart: '重新開始旅程',
        btnToMission: '返回主頁',
      },
      en: {
        appTitle: 'Industrial Revolution Quest',
        appSubtitle: 'Game-based history mission · Why did Britain lead first?',
        progress: 'Progress',
        start: 'Start',
        backCover: 'Back to cover',
        missionTitle: 'Mission',
        missionIntro: 'We will unlock the stories of Thomas, Mary, and William through questions. Each correct answer advances the story and earns a badge. Collect the six conditions—Raw Materials, Labor, Capital, Market, Division of Labor, and Technology—to explain why the Industrial Revolution began in Britain.',
        enterCh1: 'Enter Chapter 1 (Thomas)',
        thinkQ: 'Think: What would life be like without machines?',
        achievements: 'Achievements',
        achievementsNote: 'Collect all six to unlock the finale',
        badgeLabels: { '原料':'Raw Materials', '勞動力':'Labor', '資金':'Capital', '市場':'Market', '分工':'Division of Labor', '科技':'Technology' },
        statusDone: 'Done',
        statusProgress: 'In progress',
        statusLocked: '🔒 Locked',
        storyTags: ['(Labor, Raw Materials)','(Division of Labor)','(Capital, Market, Technology, Resources)'],
        btnContinue: 'Continue',
        btnConfirm: 'Check',
        btnHome: 'Home',
        btnBackStep: 'Step Back',
        btnSubmitView: 'Submit & View Reference',
        savedThought: '🗒️ Saved your thoughts. Let’s move on!',
        toastUnlocked: '🔓 Next chapter unlocked!',
        toastLocked: '🔒 Locked',
        toastAch: (name)=>`✔️ Badge unlocked: ${name}`,
        qDifficulty: 'Difficulty',
        qPrompt: 'Question',
        openShare: 'Share my thoughts',
        coverTitle: 'Why did the Industrial Revolution start in Britain?',
        missionCard: ['Raw Materials','Labor','Capital','Market','Division of Labor','Technology'],
        summaryControlsNote: 'Complete all steps to finish',
        summary1Title: 'Six Conditions: Unlocked!',
        summary1Text: `
📌 Mind-map summary (Center: Britain)
- 🐑 Raw Materials: wool/cotton → Thomas (enclosure→wool), William (overseas cotton)
- 👥 Labor: people moved to cities → Thomas (seeking factory work)
- 💰 Capital: profits from agriculture and commerce → William (investing in factories & machines)
- 🚢 Market: ports and global routes → William (overseas market), Mary (lower prices grew demand)
- 🧩 Division of Labor: factory workflow → Mary (coordinated steps)
- ⚙️ Technology: machinery and improvements → Mary (spinning jenny), William (societies, patents, steam)`.trim(),
        quizTitle: (n)=>`Quick Check · Q${n}`,
        tfLabel: ['True','False'],
        openTitle: (n)=>`Quick Check · Q${n}`,
        refAnswer: 'Reference Answer',
        summaryEndTitle: 'Finale',
        summaryEndText: 'These six conditions came together perfectly, letting the seed of the Industrial Revolution sprout first in Britain. 🎉 Congrats!',
        btnRestart: 'Restart',
        btnToMission: 'Home',
      }
    };

    const badgeEmojiMap = { '原料':'🐑','勞動力':'👥','資金':'💰','市場':'🚢','分工':'🧩','科技':'⚙️' };
    const badgeEmoji = n => badgeEmojiMap[n] || '🏅';
    const badgeLabelEn = { '原料':'Raw Materials','勞動力':'Labor','資金':'Capital','市場':'Market','分工':'Division of Labor','科技':'Technology' };

    // ====== State ======
    const state = {
      lang: 'zh',
      view: 'cover', // cover, mission, chapter, summary
      chapterIndex: 0,
      sceneIndex: 0,
      summaryIndex: 0,
      completedScenes: 0,
      totalScenes: 0,
      achievements: { '原料': false, '勞動力': false, '資金': false, '市場': false, '分工': false, '科技': false },
      chapterUnlocked: [true, false, false],
      chapterCompleted: [false, false, false],
    };

    // ====== Content (ZH + EN) ======
    const content = {
      zh: {
        chapters: [
          { id:'thomas', title:'第一章｜托馬斯的困境 – 農民到工人', avatar:'👨‍🌾', scenes:[
            { type:'narrative', title:'鄉間清晨的日常', text:'托馬斯是個15歲的英國鄉村少年，他與家人過著自給自足的農耕生活。每天清晨，他都要幫父親照料田裡的莊稼和家中的奶牛。雖然貧窮，但他們擁有屬於自己的小塊土地和村莊的公共草地，生活還算安定。' },
            { type:'narrative', title:'變局來臨：圈地', text:'然而，從1780年開始，村裡的大地主開始推行「圈地運動」。地主們用柵欄和樹籬將公共草地和許多像托馬斯家這樣的小農地圈佔起來，聲稱要改為更有效率的牧羊場。' },
            { type:'quiz', badge:'原料', question:'根據你的理解，圈地運動將土地圈起來主要用作什麼？這為哪個行業提供了原料？', difficulty:'★',
              options:['A) 種植棉花，為製糖業提供原料','B) 開採煤礦，為鋼鐵業提供原料','C) 興建工廠，為機器製造業提供場地','D) 作為牧場養羊，為紡織業提供原料'], correct:3,
              success:'正確！圈地運動生產了大量羊毛，正是工業革命首先從紡織業開始的原因之一。', hint:'提示：想想羊毛最直接對應哪個行業。'
            },
            { type:'narrative', title:'失地之痛', text:'托馬斯的家庭失去了賴以生存的土地，他們無法再種植糧食，連放牧奶牛的地方都沒有了。生計陷入絕境，父親整天愁眉不展。' },
            { type:'quiz', badge:'勞動力', question:'像托馬斯家這樣的農民，在圈地運動中遭受了什麼影響？', difficulty:'★★',
              options:['A) 獲得了地主的大量賠償，生活變得富裕','B) 喪失了耕地，流離失所，被迫尋找新的生計','C) 被地主聘請為牧羊人，收入穩定','D) 成功阻止了圈地運動，保住了土地'], correct:1,
              success:'是的！大量農民失去土地，成為無產者，他們只能湧向城市謀生。', hint:'提示：想想失地農民最可能去哪裡謀生。'
            },
            { type:'narrative', title:'踏上前往曼徹斯特的路', text:'為了生存，托馬斯一家只好離開故鄉，徒步前往遙遠的曼徹斯特。聽說那裡的紡織工場正在招募工人。一路上，他們遇到了許多和他們一樣命運的家庭。' },
            { type:'quiz', badge:'勞動力', question:'農民湧入城市，對工業革命產生了什麼關鍵影響？', difficulty:'★★★',
              options:['A) 他們帶來了農業技術，提高了工廠的飲食水平','B) 他們為新興的工廠提供了急需的充足廉價勞動力','C) 他們導致城市人口減少，市場萎縮','D) 他們發明了新的機器，取代了手工勞動'], correct:1,
              success:'答對了！城市因此擁有源源不絕、成本較低的勞動力，工廠得以迅速擴張。', hint:'提示：思考城市化如何支撐工廠擴展。'
            },
            { type:'open', title:'想一想', prompt:'如果你是托馬斯，面對失去土地與進城打工，你最擔心的是什麼？你也看到了哪些新機會？請寫下你的想法。', placeholder:'輸入你的想法…',
              modelAnswer:'可能會擔心收入不穩、居住擁擠與工時長，但也能在城市學習新技能、獲得現金工資與接觸更多機會。'
            },
            { type:'narrative', title:'曼徹斯特的煙囪', text:'托馬斯終於來到曼徹斯特。高聳的煙囪與密集的工廠證明：這裡正需要大量雙手。對他而言，這趟遷徙雖然艱難，但也揭示了工業革命的其中兩項條件：原料（羊毛）與勞動力（進城的人）。' },
          ]},
          { id:'mary', title:'第二章｜瑪麗的見證 – 家庭到工場', avatar:'🧵', scenes:[
            { type:'narrative', title:'家中的燭光', text:'瑪麗和母親在昏暗的家裡紡紗與織布，這是典型的家庭手工業。她們按件交貨，收入微薄且不穩定。' },
            { type:'narrative', title:'走入手工工場', text:'不久，鎮上出現了由商人主導的手工工場：工人集中在一棟大樓裡，材料與工具由商人提供，工作步驟被切分。' },
            { type:'quiz', badge:'分工', question:'以下哪一項最能描述工場制度下的特色？', difficulty:'★',
              options:['A) 每個人從頭到尾獨自完成一件衣服','B) 工作被拆分成步驟，由多人協作完成','C) 工人只在家中工作，不需要工場','D) 沒有固定時間與規範'], correct:1,
              success:'正確！工場推行分工協作，不同工人負責不同步驟，效率更高。', hint:'提示：留意「分工」如何提升產量。'
            },
            { type:'narrative', title:'流程的力量', text:'在工場裡，棉花被送入，一步步從紡紗、織布到成衣。分工使每個人專注於單一步驟，速度顯著提升。' },
            { type:'quiz', badge:'科技', question:'「珍妮紡紗機」的關鍵改變是什麼？', difficulty:'★★',
              options:['A) 讓布料更花俏，但效率下降','B) 同時紡出多條紗線，生產力大增','C) 完全不需要勞動力','D) 只能在家中使用'], correct:1,
              success:'沒錯！多紗錠同時運轉，大幅提升紡紗速度，成為機械化的重要一步。', hint:'提示：想想為何同時紡出多條紗線會影響效率。'
            },
            { type:'narrative', title:'操作機器的新日常', text:'瑪麗開始學習操作珍妮紡紗機。雖然工作更緊湊，但產量倍增，工場也更願意僱用熟練的女工。' },
            { type:'quiz', badge:'市場', question:'分工與機械化對紡織品的價格與市場有何影響？', difficulty:'★★★',
              options:['A) 成本上升，市場縮小','B) 成本下降，價格更低，市場擴大','C) 價格不變，但工人更輕鬆','D) 價格更高，但更時尚'], correct:1,
              success:'答對！成本下降讓更多人買得起布匹與衣物，國內外市場一同擴大。', hint:'提示：思考機械化如何改變供需與價格。'
            },
            { type:'open', title:'換位思考', prompt:'如果你是瑪麗，面對分工與機械化，你最期待或擔心什麼？請寫下你的想法。', placeholder:'輸入你的看法…',
              modelAnswer:'分工讓生產更快、收入更穩；但也可能工作單調、節奏更緊，需學會與機器協作與適應紀律。'
            },
            { type:'narrative', title:'望向更大的工廠', text:'站在窗邊，瑪麗看見更龐大的工廠與更規模化的合作。她明白了：分工協作、機器技術與擴大的市場，是推動產量暴增的關鍵力量。' },
          ]},
          { id:'william', title:'第三章｜威廉的視野 – 資本與創新', avatar:'👨‍💼', scenes:[
            { type:'narrative', title:'來自牧場的積蓄', text:'威廉出身貴族商人家庭，經營大片牧場。羊毛與農業改良帶來穩定收益，逐漸累積可投資的資金。' },
            { type:'quiz', badge:'資金', question:'英國早期的哪裡為工業化提供了重要的投資來源？', difficulty:'★',
              options:['A) 大量的政府獎金與免費機器','B) 農業改良與商業利潤積累的私人資金','C) 完全依靠外國貸款','D) 工人自願無薪投入'], correct:1,
              success:'正確！來自農業改良與貿易的利潤，成為投資工廠與機器的關鍵資金。', hint:'提示：想想資金從何而來、如何投入工廠。'
            },
            { type:'narrative', title:'港口的繁忙', text:'英國擁有眾多良港，貨船不斷裝卸棉花與各地商品，商路四通八達。' },
            { type:'quiz', badge:'市場', question:'為何英國在18世紀擁有龐大的市場？', difficulty:'★★',
              options:['A) 島內人口減少，需求下降','B) 海港眾多、海軍強大與殖民地網絡，拓展海外市場','C) 禁止對外貿易，專注內銷','D) 主要靠禮物交換，不涉及購買'], correct:1,
              success:'沒錯！海上優勢與殖民網絡連接全球，國內外市場同時擴張。', hint:'提示：思考海運與殖民如何擴展需求。'
            },
            { type:'narrative', title:'學會與專利的力量', text:'在皇家學會等機構中，威廉與科學家交流。英國的專利制度保護發明，鼓勵投資與試驗。' },
            { type:'quiz', badge:'科技', question:'哪一項制度最有助於鼓勵技術創新？', difficulty:'★★★',
              options:['A) 禁止技術交流，保密至上','B) 取消所有發明者的權利','C) 專利制度與學會交流，共同推動改良','D) 完全依靠運氣與靈感'], correct:2,
              success:'答得好！專利與學術交流讓創新者得到回報，也讓知識快速擴散與改進。', hint:'提示：想想保障發明者權利與交流如何促進創新。'
            },
            { type:'open', title:'投資選擇', prompt:'如果你握有資金，你會投資哪項新技術或基礎建設？請說說原因。', placeholder:'輸入你的想法…',
              modelAnswer:'可考慮投資蒸汽機改良、運河與鐵路建設、港口與紡織機械，能擴大產能、降低運輸成本並拓展市場。'
            },
            { type:'narrative', title:'蒸汽機的轟鳴', text:'威廉最終投資改良蒸汽機與運輸。煤與鐵等資源，加上資金、市場與技術，讓工業革命在英國率先騰飛。' },
          ]},
        ],
        summary: [
          { type:'narrative', title:'六大條件，解鎖成功！', text:ui.zh.summary1Text },
          { type:'quiz', title:ui.zh.quizTitle(1), question:'圈地運動最直接地為工業革命提供了什麼條件？',
            options:['A) 廣闊的海外市場','B) 充足的勞動力與羊毛原料','C) 大量資金','D) 新的交通工具'], correct:1,
            success:'答對！圈地帶來羊毛與城市勞動力兩大關鍵。', hint:'提示：回想托馬斯一家與羊毛。'
          },
          { type:'truefalse', title:ui.zh.quizTitle(2), question:'判斷題：英國政府通過的《壟斷法》是為了打擊發明，保護傳統手工業。（對 / 錯）',
            options:ui.zh.tfLabel, correct:1, success:'正確答案是「錯」。英國透過專利等制度鼓勵發明與改良。', hint:'提示：想想創新者權益是否受到保護。'
          },
          { type:'open', title:ui.zh.openTitle(3), prompt:'在手工工場裡，工人實行「分工協作」，這帶來了什麼好處？（簡答）', modelAnswer:'分工提高了生產效率，並為發明與改良機器提供了啟發。' },
          { type:'end', title:'結語', text:ui.zh.summaryEndText },
        ]
      },
      en: {
        chapters: [
          { id:'thomas', title:'Chapter 1 · Thomas — From Farmer to Worker', avatar:'👨‍🌾', scenes:[
            { type:'narrative', title:'Morning in the Countryside', text:'Thomas was 15 and lived in rural Britain. His family lived by farming and made many things they needed. Each morning he helped his father with the fields and the cows. They were poor, but they still had a small plot and could use the village common land, so life was fairly steady.' },
            { type:'narrative', title:'A Turning Point: Enclosure', text:'Around 1780, local landlords started “enclosure”. They put up fences and hedges to close the common land and many small plots like Thomas’s. They wanted to raise sheep more efficiently.' },
            { type:'quiz', badge:'原料', question:'What was the enclosed land used for, and which industry got materials from it?', difficulty:'★',
              options:['A) Growing cotton for the sugar industry','B) Mining coal for iron and steel','C) Building factories for machine-making','D) Sheep pastures providing wool for textiles'], correct:3,
              success:'Correct! Enclosure produced abundant wool, helping textiles lead the revolution.', hint:'Hint: Which industry uses wool most directly?'
            },
            { type:'narrative', title:'Losing the Land', text:'Thomas’s family lost their land. They could not grow food or keep their cows anymore. Their way of living broke down, and everyone was worried.' },
            { type:'quiz', badge:'勞動力', question:'Under enclosure, what happened to many farmers like Thomas’s family?', difficulty:'★★',
              options:['A) They received large compensation and became wealthy','B) They lost land, were displaced, and had to find new livelihoods','C) They were hired as shepherds with stable pay','D) They stopped enclosure and kept their land'], correct:1,
              success:'Yes. Many became landless and moved to cities for work.', hint:'Hint: Where would landless farmers most likely go?'
            },
            { type:'narrative', title:'Road to Manchester', text:'To survive, Thomas’s family left home and walked to faraway Manchester. They heard that textile workshops were hiring. Many other families did the same.' },
            { type:'quiz', badge:'勞動力', question:'What was the key effect of rural migrants on the Industrial Revolution?', difficulty:'★★★',
              options:['A) They brought farming skills to improve factory meals','B) They supplied abundant, low-cost labor to new factories','C) They reduced city populations and shrank markets','D) They invented machines replacing manual work'], correct:1,
              success:'Right! Cities gained steady, lower-cost labor, fueling rapid factory growth.', hint:'Hint: How does urbanization support factory expansion?'
            },
            { type:'open', title:'Think About It', prompt:'If you were Thomas, what would worry you most after losing land, and what new chances might you see in the city?', placeholder:'Type your thoughts…',
              modelAnswer:'You might fear unstable income, crowded housing, and long hours; yet cities offer new skills, cash         document.getElementById('summaryNext').onclick=e=>{ e.preventDefault(); nextSummary(); };
      }
      if(sc.type==='quiz'){
        const form=document.getElementById('sQuizForm');
        const fb=document.getElementById('sQuizFeedback');
        document.getElementById('sCheck').onclick=e=>{
          e.preventDefault();
          const v=new FormData(form).get('opt'); if(v===null){ fb.innerHTML=`<span class="text-slate-600">${state.lang==='zh'?'請先選擇一個答案。':'Please choose an option first.'}</span>`; return; }
          if(parseInt(v,10)===sc.correct){ fb.innerHTML=`<div class="text-emerald-700">${sc.success}</div>`; setTimeout(nextSummary,600); }
          else { fb.innerHTML=`<div class="text-amber-700">${sc.hint}</div>`; }
        };
      }
      if(sc.type==='truefalse'){
        const form=document.getElementById('sTFForm');
        const fb=document.getElementById('sTFFeedback');
        document.getElementById('sTFCheck').onclick=e=>{
          e.preventDefault();
          const v=new FormData(form).get('tf'); if(v===null){ fb.innerHTML=`<span class="text-slate-600">${state.lang==='zh'?'請先選擇「對」或「錯」。':'Please choose True or False.'}</span>`; return; }
          if(parseInt(v,10)===sc.correct){ fb.innerHTML=`<div class="text-emerald-700">${sc.success}</div>`; setTimeout(nextSummary,600); }
          else { fb.innerHTML=`<div class="text-amber-700">${sc.hint}</div>`; }
        };
      }
      if(sc.type==='open'){
        document.getElementById('sOpenSubmit').onclick=e=>{
          e.preventDefault();
          const fb=document.getElementById('sOpenFeedback');
          fb.innerHTML=`<div class="surface-soft p-3 rounded-lg mt-2"><div class="font-bold mb-1">${ui[state.lang].refAnswer}</div><div>${sc.modelAnswer}</div><div class="mt-3"><button id="sOpenContinue" class="btn btn-primary">${ui[state.lang].btnContinue}</button></div></div>`;
          document.getElementById('sOpenContinue').onclick = ev=>{ ev.preventDefault(); nextSummary(); };
        };
      }
      if(sc.type==='end'){
        document.getElementById('restart').onclick=e=>{ e.preventDefault(); resetGame(); };
        document.getElementById('toMissionFromEnd').onclick=e=>{ e.preventDefault(); state.view='mission'; render(); };
      }
    }

    // ====== Language Buttons ======
    document.getElementById('langZh').addEventListener('click', (e)=>{ e.preventDefault(); state.lang='zh'; render(); });
    document.getElementById('langEn').addEventListener('click', (e)=>{ e.preventDefault(); state.lang='en'; render(); });

    // Init
    render();
    window.addEventListener('submit', e=> e.preventDefault());
  </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'981da4e7a67add62',t:'MTc1ODMzMjczNC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
