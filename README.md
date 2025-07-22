<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>千葉イベントカレンダー</title>
    <!-- Tailwind CSS読み込み -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* フォント設定 */
        @import url('https://fonts.googleapis.com/css2?family=Hachi+Maru+Pop&display=swap');
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap');

        body {
            font-family: 'Hachi Maru Pop', 'Inter', sans-serif;
            background-color: #FFFFFF;
            color: #333;
        }
        /* カラー */
        .text-custom-orange { color: #FF6600; }
        .bg-custom-orange { background-color: #FF6600; }
        .border-custom-orange { border-color: #FF6600; }

        /* スライダー */
        .slider-container {
            overflow: hidden;
            position: relative;
            width: 100%;
            border-radius: 1rem;
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        }
        .slider-wrapper {
            display: flex;
            transition: transform 0.7s cubic-bezier(0.25, 0.46, 0.45, 0.94);
        }
        .slider-item {
            min-width: 100%;
            flex-shrink: 0;
            height: 520px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 1rem;
            overflow: hidden;
            position: relative;
        }
        .slider-item img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
        }
        .slider-item-text {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: white;
            font-size: 2.5rem;
            font-weight: 800;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
            font-family: 'Hachi Maru Pop', cursive;
            text-align: center;
            z-index: 2;
        }
        /* スライダークリッカー */
        .slider-nav-button {
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
            background-color: rgba(0, 0, 0, 0.6);
            color: white;
            padding: 1rem 1.25rem;
            border-radius: 9999px;
            cursor: pointer;
            z-index: 10;
            transition: background-color 0.3s ease, transform 0.3s ease;
            opacity: 0.8;
        }
        .slider-nav-button:hover {
            background-color: rgba(0, 0, 0, 0.8);
            transform: translateY(-50%) scale(1.1);
            opacity: 1;
        }
        .slider-nav-button.left { left: 1.5rem; }
        .slider-nav-button.right { right: 1.5rem; }
        /* スライダードット */
        .slider-dots {
            position: absolute;
            bottom: 1.5rem;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 0.75rem;
            z-index: 10;
        }
        .slider-dot {
            width: 14px;
            height: 14px;
            background-color: rgba(255, 255, 255, 0.6);
            border-radius: 9999px;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.3s ease;
        }
        .slider-dot.active {
            background-color: white;
            transform: scale(1.3);
            box-shadow: 0 0 0 3px rgba(255,255,255,0.5);
        }

        /* カレンダー専用スタイル */
        .calendar-month {
            margin-bottom: 2rem;
            border: 1px solid #e2e8f0; /* Tailwind's gray-200 */
            border-radius: 0.75rem; /* Tailwind's rounded-xl */
            overflow: hidden;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06); /* Tailwind's shadow-md */
            background-color: #fff;
        }
        .calendar-header {
            background-color: #FF6600; /* Custom orange */
            color: white;
            padding: 1rem;
            text-align: center;
            font-weight: bold;
            font-size: 1.5rem;
        }
        .calendar-weekdays {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            text-align: center;
            font-weight: 600;
            color: #4a5568; /* Tailwind's gray-700 */
            border-bottom: 1px solid #edf2f7; /* Tailwind's gray-100 */
            padding-bottom: 0.5rem;
            padding-top: 0.5rem;
            background-color: #f7fafc; /* Tailwind's gray-50 */
        }
        .calendar-grid {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            border-top: 1px solid #edf2f7; /* Tailwind's gray-100 */
        }
        .calendar-day {
            min-height: 100px; /* Adjust as needed */
            border: 1px solid #edf2f7; /* Tailwind's gray-100 */
            padding: 0.5rem;
            position: relative;
            display: flex;
            flex-direction: column;
            align-items: flex-start;
            overflow: hidden;
        }
        .calendar-day.empty {
            background-color: #f7fafc; /* Tailwind's gray-50 */
        }
        .day-number {
            font-weight: bold;
            font-size: 1.25rem;
            color: #2d3748; /* Tailwind's gray-800 */
            margin-bottom: 0.25rem;
        }
        .event-item {
            background-color: #fed7aa; /* Tailwind's orange-200 */
            color: #c05621; /* Tailwind's orange-800 */
            font-size: 0.875rem; /* Tailwind's text-sm */
            padding: 0.25rem 0.5rem;
            border-radius: 0.25rem; /* Tailwind's rounded-md */
            margin-bottom: 0.25rem;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            width: 100%;
            cursor: pointer;
            transition: background-color 0.2s ease;
        }
        .event-item:hover {
            background-color: #fbbf24; /* Tailwind's amber-400 */
            color: #7c2d12; /* Tailwind's amber-900 */
        }

        /* ポップアップ（モーダル）のスタイル */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }
        .modal-overlay.show {
            opacity: 1;
            visibility: visible;
        }
        .modal-content {
            background-color: white;
            padding: 2rem;
            border-radius: 1rem;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
            max-width: 90%;
            max-height: 90%;
            overflow-y: auto;
            position: relative;
            transform: translateY(20px);
            transition: transform 0.3s ease;
        }
        .modal-overlay.show .modal-content {
            transform: translateY(0);
        }
        .modal-close-button {
            position: absolute;
            top: 1rem;
            right: 1rem;
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            color: #4a5568; /* Tailwind's gray-700 */
            transition: color 0.2s ease;
        }
        .modal-close-button:hover {
            color: #FF6600; /* Custom orange */
        }
        .modal-title {
            font-size: 2rem;
            font-weight: bold;
            color: #FF6600; /* Custom orange */
            margin-bottom: 1rem;
        }
        .modal-subtitle {
            font-size: 1.25rem;
            color: #4a5568; /* Tailwind's gray-700 */
            margin-bottom: 0.75rem;
        }
        .modal-detail-item {
            margin-bottom: 0.5rem;
            font-size: 1rem;
            color: #2d3748; /* Tailwind's gray-800 */
        }
        .modal-detail-label {
            font-weight: 600;
            color: #4a5568; /* Tailwind's gray-700 */
            margin-right: 0.5rem;
        }
        .modal-detail-link {
            color: #3182ce; /* Tailwind's blue-500 */
            text-decoration: underline;
        }
    </style>
</head>
<body class="antialiased">
    <!-- ヘッダー -->
    <header class="w-full py-3 sm:py-4 flex justify-center items-center bg-amber-50 shadow-md">
        <div class="w-full max-w-6xl flex justify-between items-center px-4 sm:px-6 lg:px-8">
            <!-- ロゴとサイト名 -->
            <div class="flex items-center">
                <img src="uploaded:スクリーンショット 2025-07-21 11.49.01.png-fb3e9469-41eb-4f89-9cb3-3d58ddbaa680" alt="Chiba Event Calendarロゴ" class="w-auto h-4 sm:h-5 object-contain mr-2">
                <span class="text-base sm:text-lg font-bold text-custom-orange font-hachi-maru-pop hidden sm:block">Chiba Event Calendar</span>
            </div>
            <!-- ナビゲーション -->
            <nav class="flex space-x-2 sm:space-x-4">
                <a href="saikyou.html" class="text-gray-700 hover:text-indigo-700 font-semibold transition duration-300 py-2 px-2 sm:px-3 rounded-full hover:bg-indigo-50 transform hover:scale-105">ホーム</a>
                <a href="betupe-zi.html" class="text-gray-700 hover:text-indigo-700 font-semibold transition duration-300 py-2 px-2 sm:px-3 rounded-full hover:bg-indigo-50 transform hover:scale-105">イベントを掲載したい方はこちら</a>
            </nav>
        </div>
    </header>

    <!-- メインコンテンツ -->
    <div class="min-h-screen flex flex-col items-center p-4 sm:p-6 lg:p-8 mt-10">

        <!-- スライドショー -->
        <section class="w-full mb-12">
            <div class="slider-container">
                <div class="slider-wrapper" id="slider-wrapper">
                    <!-- スライド1 -->
                    <div class="slider-item">
                        <img src="https://placehold.co/1200x520/FF6600/FFFFFF?text=千葉の祭り" alt="千葉の祭り" onerror="this.onerror=null;this.src='https://placehold.co/1200x520/FF6600/FFFFFF?text=';">
                        <span class="slider-item-text">千葉の祭り</span>
                    </div>
                    <!-- スライド2 -->
                    <div class="slider-item">
                        <img src="https://placehold.co/1200x520/FF9933/FFFFFF?text=自然とアクティビティ" alt="自然とアクティビティ" onerror="this.onerror=null;this.src='https://placehold.co/1200x520/FF9933/FFFFFF?text=';">
                        <span class="slider-item-text">自然とアクティビティ</span>
                    </div>
                    <!-- スライド3 -->
                    <div class="slider-item">
                        <img src="https://placehold.co/1200x520/FFCC66/FFFFFF?text=文化と芸術" alt="文化と芸術" onerror="this.onerror=null;this.src='https://placehold.co/1200x520/FFCC66/FFFFFF?text=';">
                        <span class="slider-item-text">文化と芸術</span>
                    </div>
                </div>
                <!-- スライドボタン -->
                <button class="slider-nav-button left" id="prev-slide">&#10094;</button>
                <button class="slider-nav-button right" id="next-slide">&#10095;</button>
                <!-- ドットナビゲーション -->
                <div class="slider-dots" id="slider-dots">
                    <span class="slider-dot active" data-index="0"></span>
                    <span class="slider-dot" data-index="1"></span>
                    <span class="slider-dot" data-index="2"></span>
                </div>
            </div>
        </section>

        <!-- メインタイトル -->
        <section class="w-full max-w-6xl text-center mb-8">
            <h2 class="text-6xl font-extrabold text-custom-orange mb-8 tracking-tight">Chiba Event Calendar</h2>
            <div class="w-full flex justify-center items-center my-8">
                <hr class="border-t-4 border-green-500 w-1/5 sm:w-1/6 rounded-full mx-4 opacity-75">
                <p class="text-4xl font-bold text-custom-orange whitespace-nowrap drop-shadow-md">千葉のイベント情報カレンダー</p>
                <hr class="border-t-4 border-green-500 w-1/5 sm:w-1/6 rounded-full mx-4 opacity-75">
            </div>
        </section>

        <!-- カレンダーセクション（スプレッドシート連携） -->
        <section class="w-full max-w-6xl p-8 sm:p-10 mb-12">
            <h3 class="text-3xl font-bold text-custom-orange mb-8 text-center">カレンダー（スプレッドシートから読み込み）</h3>
            <div class="bg-custom-orange bg-opacity-10 p-8 rounded-xl border border-custom-orange border-opacity-40">
                <p class="text-xl text-gray-700 text-center mb-6 leading-relaxed">
                    ここにスプレッドシートから読み込んだイベントを卓上カレンダー形式で表示します。
                </p>
                <!-- カレンダーナビゲーション -->
                <div class="flex justify-between items-center mb-4">
                    <button id="prevMonth" class="bg-custom-orange hover:bg-opacity-90 text-white font-bold py-2 px-4 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-custom-orange focus:ring-opacity-50">
                        &lt; 前の月
                    </button>
                    <h4 id="currentMonthYear" class="text-2xl font-bold text-custom-orange"></h4>
                    <button id="nextMonth" class="bg-custom-orange hover:bg-opacity-90 text-white font-bold py-2 px-4 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-custom-orange focus:ring-opacity-50">
                        次の月 &gt;
                    </button>
                </div>
                <!-- カレンダー表示エリア -->
                <div id="event-calendar" class="min-h-80">
                    <p class="text-gray-500 text-center text-2xl font-medium">イベントデータを読み込み中...</p>
                </div>
            </div>
        </section>

        <!-- サイト紹介 -->
        <section class="w-full max-w-6xl p-8 sm:p-10 mb-12">
            <h3 class="text-4xl font-bold text-custom-orange mb-6 text-center">私たちのサイトについて</h3>
            <p class="text-xl leading-relaxed text-custom-orange text-center font-medium">
                ちばイベントカレンダーは、<br>
                千葉の様々なイベントがすぐでわかります。<br>
                ぜひ行きたいイベントを見つけてください。
            </p>
        </section>

        <!-- おすすめイベント・スポット -->
        <section class="w-full max-w-6xl p-8 sm:p-10 mb-12">
            <h3 class="text-3xl font-bold text-indigo-700 mb-8 text-center">おすすめイベント・スポット</h3>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
                <!-- カード1 -->
                <a href="https://example.com/link1" target="_blank" class="block rounded-xl shadow-lg overflow-hidden transform hover:scale-105 transition duration-300 ease-in-out border-2 border-transparent hover:border-indigo-400">
                    <img src="https://placehold.co/400x250/FF6600/FFFFFF?text=千葉の観光スポット" alt="外部リンク画像1" class="w-full h-56 object-cover">
                    <div class="p-5 bg-gray-50 text-center">
                        <p class="text-xl font-semibold text-gray-800 mb-1">千葉の観光スポット</p>
                        <p class="text-base text-indigo-600 font-medium hover:underline">詳細はこちら &rarr;</p>
                    </div>
                </a>
                <!-- カード2 -->
                <a href="https://example.com/link2" target="_blank" class="block rounded-xl shadow-lg overflow-hidden transform hover:scale-105 transition duration-300 ease-in-out border-2 border-transparent hover:border-indigo-400">
                    <img src="https://placehold.co/400x250/FF9933/FFFFFF?text=地元のグルメフェス" alt="外部リンク画像2" class="w-full h-56 object-cover">
                    <div class="p-5 bg-gray-50 text-center">
                        <p class="text-xl font-semibold text-gray-800 mb-1">地元のグルメフェス</p>
                        <p class="text-base text-indigo-600 font-medium hover:underline">詳細はこちら &rarr;</p>
                    </div>
                </a>
                <!-- カード3 -->
                <a href="https://example.com/link3" target="_blank" class="block rounded-xl shadow-lg overflow-hidden transform hover:scale-105 transition duration-300 ease-in-out border-2 border-transparent hover:border-indigo-400">
                    <img src="https://placehold.co/400x250/FFCC66/FFFFFF?text=アートイベント情報" alt="外部リンク画像3" class="w-full h-56 object-cover">
                    <div class="p-5 bg-gray-50 text-center">
                        <p class="text-base text-indigo-600 font-medium hover:underline">詳細はこちら &rarr;</p>
                    </div>
                </a>
            </div>
        </section>

        <!-- フッター -->
        <footer class="w-full max-w-6xl bg-gray-900 text-white shadow-xl rounded-2xl p-6 sm:p-8 text-center mt-8 mb-4">
            <p class="mb-3 text-lg">&copy; 2025 千葉イベントカレンダー. All rights reserved.</p>
            <div class="flex justify-center space-x-6 text-base">
                <a href="#" class="text-gray-400 hover:text-white transition duration-300 hover:underline">プライバシーポリシー</a>
                <a href="#" class="text-gray-400 hover:text-white transition duration-300 hover:underline">利用規約</a>
            </div>
        </footer>
    </div>

    <!-- イベント詳細ポップアップ（モーダル） -->
    <div id="eventModal" class="modal-overlay">
        <div class="modal-content">
            <button class="modal-close-button" id="closeModal">&times;</button>
            <h3 id="modalEventTitle" class="modal-title"></h3>
            <p id="modalEventSubtitle" class="modal-subtitle"></p>
            <div class="modal-details text-left">
                <p class="modal-detail-item"><span class="modal-detail-label">日時:</span> <span id="modalEventDateTime"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">終了日時:</span> <span id="modalEventEndTime"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">会場:</span> <span id="modalEventVenue"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">住所:</span> <span id="modalEventAddress"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">エリア:</span> <span id="modalEventArea"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">おすすめ:</span> <span id="modalEventRecommended"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">ホームページ:</span> <span id="modalEventHomepage"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">会場アクセス:</span> <span id="modalEventAccessInfo"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">補足:</span> <span id="modalEventSupplement"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">予備日:</span> <span id="modalEventReserveDate"></span></p>
                <p class="modal-detail-item"><span class="modal-detail-label">イベントアクセス:</span> <span id="modalEventEventAccess"></span></p>
            </div>
        </div>
    </div>

    <script>
        // スライドショーJavaScript
        const sliderWrapper = document.getElementById('slider-wrapper');
        const prevButton = document.getElementById('prev-slide');
        const nextButton = document.getElementById('next-slide');
        const sliderDots = document.getElementById('slider-dots');
        const slides = document.querySelectorAll('.slider-item');
        let currentIndex = 0;
        const slideIntervalTime = 5000;

        function updateSlider() {
            sliderWrapper.style.transform = `translateX(${-currentIndex * 100}%)`;
            document.querySelectorAll('.slider-dot').forEach((dot, index) => {
                dot.classList.toggle('active', index === currentIndex);
            });
        }

        function nextSlide() {
            currentIndex = (currentIndex + 1) % slides.length;
            updateSlider();
        }

        function prevSlide() {
            currentIndex = (currentIndex - 1 + slides.length) % slides.length;
            updateSlider();
        }

        sliderDots.addEventListener('click', (event) => {
            if (event.target.classList.contains('slider-dot')) {
                currentIndex = parseInt(event.target.dataset.index);
                updateSlider();
                resetAutoSlide();
            }
        });

        prevButton.addEventListener('click', () => {
            prevSlide();
            resetAutoSlide();
        });
        nextButton.addEventListener('click', () => {
            nextSlide();
            resetAutoSlide();
        });

        let autoSlideInterval = setInterval(nextSlide, slideIntervalTime);

        function resetAutoSlide() {
            clearInterval(autoSlideInterval);
            autoSlideInterval = setInterval(nextSlide, slideIntervalTime);
        }

        // カレンダー関連のグローバル変数
        let allEvents = []; // 全イベントデータを格納する配列
        let currentCalendarDate = new Date(); // 現在表示しているカレンダーの年月

        // Google Apps Scriptからイベントデータ読み込み
        async function fetchEventsFromGoogleSheet() {
            const eventCalendarDiv = document.getElementById('event-calendar');
            eventCalendarDiv.innerHTML = '<p class="text-gray-500 text-center text-2xl font-medium">イベントデータを読み込み中...</p>';

            const gasWebAppUrl = 'https://script.google.com/macros/s/AKfycbwQvwnf5iCnUSBF7q3FK6TWHyDG11J5qrJnKMAU9gfYsV3pd_MWD7LTWpX_wK-8unU3fQ/exec'; 

            if (!gasWebAppUrl.startsWith('https://script.google.com/macros/s/') || !gasWebAppUrl.endsWith('/exec')) {
                eventCalendarDiv.innerHTML = '<p class="text-red-500 text-center text-xl font-medium">イベントの読み込み中に問題が発生しました。後でもう一度お試しください。</p>';
                console.error('Google Apps ScriptのURLが不正な形式です。');
                return;
            }

            try {
                const response = await fetch(gasWebAppUrl);
                if (!response.ok) {
                    const errorText = await response.text();
                    console.error(`HTTPエラー: ${response.status} ${response.statusText}`, errorText);
                    throw new Error(`データ取得失敗 (ステータス: ${response.status}). 詳細: ${errorText.substring(0, 100)}...`);
                }
                allEvents = await response.json(); // 取得した全イベントを保存

                // イベントを日付でソート
                allEvents.sort((a, b) => {
                    const dateA = new Date(a['日時']);
                    const dateB = new Date(b['日時']);
                    return dateA - dateB;
                });

                // カレンダーを初期表示
                renderCalendar(currentCalendarDate.getFullYear(), currentCalendarDate.getMonth());

            } catch (error) {
                console.error('GASデータ読み込み/解析エラー:', error);
                eventCalendarDiv.innerHTML = '<p class="text-red-500 text-center text-xl font-medium">イベントの読み込み中に問題が発生しました。後でもう一度お試しください。</p>';
            }
        }

        // カレンダーを描画する関数
        function renderCalendar(year, month) {
            const eventCalendarDiv = document.getElementById('event-calendar');
            const currentMonthYearHeader = document.getElementById('currentMonthYear');
            
            // カレンダー表示をクリア
            eventCalendarDiv.innerHTML = '';

            // 現在の年月を表示
            currentMonthYearHeader.textContent = new Date(year, month).toLocaleString('ja-JP', { month: 'long', year: 'numeric' });

            const monthContainer = document.createElement('div');
            monthContainer.className = 'calendar-month';
            monthContainer.innerHTML = `
                <div class="calendar-weekdays">
                    <span>日</span><span>月</span><span>火</span><span>水</span><span>木</span><span>金</span><span>土</span>
                </div>
                <div class="calendar-grid" id="grid-${year}-${month}"></div>
            `;
            eventCalendarDiv.appendChild(monthContainer);

            const grid = monthContainer.querySelector('.calendar-grid');
            const firstDayOfMonth = new Date(year, month, 1);
            const lastDayOfMonth = new Date(year, month + 1, 0);
            const numDaysInMonth = lastDayOfMonth.getDate();
            const firstDayOfWeek = firstDayOfMonth.getDay(); // 0 for Sunday, 1 for Monday, etc.

            // 前月の空白セルを生成
            for (let i = 0; i < firstDayOfWeek; i++) {
                const emptyDay = document.createElement('div');
                emptyDay.className = 'calendar-day empty';
                grid.appendChild(emptyDay);
            }

            // 各日付のセルを生成し、イベントを配置
            for (let day = 1; day <= numDaysInMonth; day++) {
                const dayCell = document.createElement('div');
                dayCell.className = 'calendar-day';
                dayCell.innerHTML = `<div class="day-number">${day}</div>`;
                
                // 現在の年月日のイベントをフィルタリング
                const currentDayEvents = allEvents.filter(event => {
                    try {
                        const eventDate = new Date(event['日時']);
                        return eventDate.getFullYear() === year && 
                               eventDate.getMonth() === month && 
                               eventDate.getDate() === day;
                    } catch (e) {
                        console.warn('日付の解析エラーをスキップ:', event['日時'], e);
                        return false;
                    }
                });

                currentDayEvents.forEach(event => {
                    const eventItem = document.createElement('div');
                    eventItem.className = 'event-item';
                    eventItem.textContent = event['タイトル'] || 'イベント';
                    // イベントデータを直接要素に保持
                    eventItem.eventData = event; 
                    eventItem.addEventListener('click', () => showEventDetails(eventItem.eventData)); // クリックイベントを追加
                    dayCell.appendChild(eventItem);
                });
                grid.appendChild(dayCell);
            }

            // イベントがない月の場合はメッセージを表示
            if (allEvents.length === 0 && grid.children.length === 0) {
                eventCalendarDiv.innerHTML = '<p class="text-gray-500 text-center text-xl font-medium">表示するイベントがありません。</p>';
            }
        }

        // カレンダーナビゲーションボタンのイベントリスナー
        document.getElementById('prevMonth').addEventListener('click', () => {
            currentCalendarDate.setMonth(currentCalendarDate.getMonth() - 1);
            renderCalendar(currentCalendarDate.getFullYear(), currentCalendarDate.getMonth());
        });

        document.getElementById('nextMonth').addEventListener('click', () => {
            currentCalendarDate.setMonth(currentCalendarDate.getMonth() + 1);
            renderCalendar(currentCalendarDate.getFullYear(), currentCalendarDate.getMonth());
        });

        // ポップアップ関連の要素取得
        const eventModal = document.getElementById('eventModal');
        const closeModalButton = document.getElementById('closeModal');
        const modalEventTitle = document.getElementById('modalEventTitle');
        const modalEventSubtitle = document.getElementById('modalEventSubtitle');
        const modalEventDateTime = document.getElementById('modalEventDateTime');
        const modalEventEndTime = document.getElementById('modalEventEndTime');
        const modalEventVenue = document.getElementById('modalEventVenue');
        const modalEventAddress = document.getElementById('modalEventAddress');
        const modalEventArea = document.getElementById('modalEventArea');
        const modalEventRecommended = document.getElementById('modalEventRecommended');
        const modalEventHomepage = document.getElementById('modalEventHomepage');
        const modalEventAccessInfo = document.getElementById('modalEventAccessInfo');
        const modalEventSupplement = document.getElementById('modalEventSupplement');
        const modalEventReserveDate = document.getElementById('modalEventReserveDate');
        const modalEventEventAccess = document.getElementById('modalEventEventAccess');

        // イベント詳細ポップアップを表示する関数
        function showEventDetails(event) {
            modalEventTitle.textContent = event['タイトル'] || 'タイトルなし';
            modalEventSubtitle.textContent = event['サブタイトル'] || 'サブタイトルなし';
            modalEventDateTime.textContent = event['日時'] || '不明';
            modalEventEndTime.textContent = event['終了日時'] || 'なし';
            modalEventVenue.textContent = event['会場'] || '不明';
            modalEventAddress.textContent = event['住所'] || '不明';
            modalEventArea.textContent = event['エリア'] || '不明';
            modalEventRecommended.textContent = event['おすすめ'] || 'なし';
            
            // ホームページはリンクとして表示
            if (event['ホームページ']) {
                modalEventHomepage.innerHTML = `<a href="${event['ホームページ']}" target="_blank" class="modal-detail-link">${event['ホームページ']}</a>`;
            } else {
                modalEventHomepage.textContent = 'なし';
            }

            modalEventAccessInfo.textContent = event['会場アクセス'] || 'なし';
            modalEventSupplement.textContent = event['補足'] || 'なし';
            modalEventReserveDate.textContent = event['予備日'] || 'なし';
            modalEventEventAccess.textContent = event['イベントアクセス'] || 'なし';

            eventModal.classList.add('show'); // ポップアップを表示
        }

        // イベント詳細ポップアップを閉じる関数
        function closeEventDetails() {
            eventModal.classList.remove('show'); // ポップアップを非表示
        }

        // ポップアップの閉じるボタンとオーバーレイにイベントリスナーを追加
        closeModalButton.addEventListener('click', closeEventDetails);
        eventModal.addEventListener('click', (e) => {
            if (e.target === eventModal) { // オーバーレイ部分をクリックした場合のみ閉じる
                closeEventDetails();
            }
        });

        // ページ読み込み時の処理
        window.onload = () => {
            updateSlider(); // スライドショーの初期表示
            fetchEventsFromGoogleSheet(); // ページ読み込み時に一度だけイベントデータを読み込み、カレンダーを表示
        };
    </script>
</body>
</html>
# poie
