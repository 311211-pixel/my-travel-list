<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>美味探索家 - 國際料理食譜收藏筆記</title>
    <style>
        :root {
            --primary-color: #ff6b6b;
            --secondary-color: #4ecdc4;
            --bg-color: #f7f9fa;
            --card-bg: #ffffff;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: #333;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        header {
            margin-bottom: 30px;
            text-align: center;
        }

        header h1 {
            color: var(--primary-color);
            font-size: 2.5rem;
            margin-bottom: 10px;
        }

        /* 頁籤切換效果 */
        .tabs {
            display: flex;
            margin-bottom: 30px;
            background: #e0e0e0;
            padding: 5px;
            border-radius: 25px;
        }

        .tab-btn {
            padding: 10px 25px;
            border: none;
            background: none;
            cursor: pointer;
            font-size: 1rem;
            font-weight: bold;
            border-radius: 20px;
            transition: 0.3s;
        }

        .tab-btn.active {
            background-color: var(--primary-color);
            color: white;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
        }

        .view-section {
            display: none;
            width: 100%;
            max-width: 600px;
        }

        .view-section.active {
            display: block;
        }

        /* 3D 卡片翻轉效果 */
        .card-container {
            perspective: 1000px;
            width: 100%;
            height: 450px;
            cursor: pointer;
            margin-bottom: 20px;
        }

        .recipe-card {
            width: 100%;
            height: 100%;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            border-radius: 15px;
        }

        .recipe-card.flipped {
            transform: rotateY(180deg);
        }

        .card-front, .card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 15px;
            background: var(--card-bg);
            padding: 25px;
            display: flex;
            flex-direction: column;
        }

        .card-front {
            align-items: center;
            justify-content: space-between;
        }

        .card-front img {
            width: 100%;
            height: 250px;
            object-fit: cover;
            border-radius: 10px;
            margin-bottom: 15px;
        }

        .badge {
            background-color: var(--secondary-color);
            color: white;
            padding: 5px 12px;
            border-radius: 15px;
            font-size: 0.85rem;
            margin-top: 5px;
        }

        .card-back {
            transform: rotateY(180deg);
            overflow-y: auto;
        }

        .card-back h3 {
            margin-bottom: 10px;
            color: var(--primary-color);
            border-bottom: 2px solid #eee;
            padding-bottom: 5px;
        }

        .card-back p {
            font-size: 0.95rem;
            line-height: 1.6;
            margin-bottom: 15px;
            white-space: pre-line;
        }

        /* 控制按鈕外觀 */
        .nav-btns {
            display: flex;
            justify-content: space-between;
            width: 100%;
            max-width: 600px;
        }

        .btn {
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            transition: 0.2s;
            font-size: 1rem;
        }

        .btn-primary { background-color: var(--primary-color); color: white; }
        .btn-secondary { background-color: #6c757d; color: white; }
        .btn:hover { opacity: 0.9; transform: translateY(-2px); }

        /* 表單設計 */
        .form-group {
            margin-bottom: 15px;
        }

        .form-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }

        .form-group input, .form-group textarea {
            width: 100%;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 6px;
            font-size: 1rem;
        }

        .api-search-box {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            background: #fff;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.05);
        }
    </style>
</head>
<body>

    <header>
        <h1>美味探索家 🍳</h1>
        <p>國際料理個人筆記與食譜卡</p>
    </header>

    <div class="tabs">
        <button class="tab-btn active" onclick="switchTab('browse')">瀏覽我的食譜卡</button>
        <button class="tab-btn" onclick="switchTab('manage')">新增料理食譜</button>
    </div>

    <div id="browse-section" class="view-section active">
        <div class="card-container" onclick="toggleFlip()">
            <div class="recipe-card" id="recipe-card">
                <div class="card-front">
                    <img id="card-img" src="https://images.unsplash.com/photo-1546069901-ba9599a7e63c?q=80&w=500" alt="料理圖片">
                    <h2 id="card-title">歡迎來到美味探索家</h2>
                    <div>
                        <span class="badge" id="card-category">點擊卡片可翻面</span>
                        <span class="badge" id="card-area">世界風味</span>
                    </div>
                </div>
                <div class="card-back">
                    <h3>🛒 所需食材</h3>
                    <p id="card-ingredients">這裡會顯示食材清單</p>
                    <h3>📝 烹飪步驟</h3>
                    <p id="card-instructions">這裡會顯示詳細的烹飪指南。趕快去切換到新增頁面，抓取你的第一道世界美食吧！</p>
                </div>
            </div>
        </div>
        <div class="nav-btns">
            <button class="btn btn-secondary" onclick="prevCard()">上一道</button>
            <button class="btn btn-primary" onclick="nextCard()">下一道</button>
        </div>
    </div>

    <div id="manage-section" class="view-section">
        <div class="api-search-box">
            <input type="text" id="api-query" placeholder="輸入料理英文名 (如: Tacos, Lasagna, Sushi)">
            <button class="btn btn-primary" onclick="fetchRecipeFromAPI()">自動填入資料</button>
        </div>

        <form id="recipe-form" onsubmit="submitToGoogleSheet(event)">
            <div class="form-group">
                <label for="form-name">料理名稱 (Dish Name)</label>
                <input type="text" id="form-name" required>
            </div>
            <div class="form-group">
                <label for="form-category">料理類別 (Category)</label>
                <input type="text" id="form-category">
            </div>
            <div class="form-group">
                <label for="form-area">國家風味 (Area/Origin)</label>
                <input type="text" id="form-area">
            </div>
            <div class="form-group">
                <label for="form-ingredients">食材清單 (Ingredients)</label>
                <textarea id="form-ingredients" rows="3" placeholder="食材項目與份量..."></textarea>
            </div>
            <div class="form-group">
                <label for="form-instructions">烹飪步驟 (Instructions)</label>
                <textarea id="form-instructions" rows="5" placeholder="詳細步驟..."></textarea>
            </div>
            <div class="form-group">
                <label for="form-image">圖片網址 (Image URL)</label>
                <input type="url" id="form-image">
            </div>
            <button type="submit" class="btn btn-primary" style="width: 100%;">儲存食譜至雲端試算表</button>
        </form>
    </div>

    <script>
        // ⚠️ 請在此處替換成你在第一步部署後取得的 Google Apps Script 網頁應用程式 URL
        const GAS_URL = "請在此貼上你的_GAS_網頁應用程式_URL";

        // 本地預設緩衝資料（當雲端無資料時顯示）
        let recipes = [
            {
                dishName: "經典風味範例",
                category: "點擊卡片可翻面",
                area: "環遊世界",
                ingredients: "熱情、程式碼、天馬行空的創意",
                instructions: "1. 打開網頁環境。\n2. 呼叫美食 API 自動填入各國料理。\n3. 點擊儲存，透過 GAS 將資料永久存到雲端試算表！",
                imageUrl: "https://images.unsplash.com/photo-1546069901-ba9599a7e63c?q=80&w=500"
            }
        ];
        let currentIndex = 0;

        // 初始化：向 GAS 讀取雲端資料
        async function loadRecipesFromCloud() {
            if(GAS_URL.includes("請在此貼上")) return;
            try {
                const response = await fetch(GAS_URL);
                const resData = await response.json();
                if(resData.result === "success" && resData.data.length > 0) {
                    recipes = resData.data;
                    currentIndex = 0;
                    renderCard();
                }
            } catch (error) {
                console.error("讀取雲端試算表失敗，使用預設資料:", error);
            }
        }

        window.onload = loadRecipesFromCloud;

        // 渲染目前卡片資料
        function renderCard() {
            const current = recipes[currentIndex];
            document.getElementById('card-title').innerText = current.dishName;
            document.getElementById('card-category').innerText = current.category || "未分類";
            document.getElementById('card-area').innerText = current.area || "世界各地";
            document.getElementById('card-ingredients').innerText = current.ingredients || "無";
            document.getElementById('card-instructions').innerText = current.instructions || "無資料";
            document.getElementById('card-img').src = current.imageUrl || "https://images.unsplash.com/photo-1546069901-ba9599a7e63c?q=80&w=500";
            
            // 重置翻牌狀態
            document.getElementById('recipe-card').classList.remove('flipped');
        }

        // 3D 翻牌控制
        function toggleFlip() {
            document.getElementById('recipe-card').classList.toggle('flipped');
        }

        // 上下一道菜切換
        function nextCard() {
            currentIndex = (currentIndex + 1) % recipes.length;
            renderCard();
        }
        function prevCard() {
            currentIndex = (currentIndex - 1 + recipes.length) % recipes.length;
            renderCard();
        }

        // 頁籤切換邏輯
        function switchTab(tab) {
            document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
            document.querySelectorAll('.view-section').forEach(sec => sec.classList.remove('active'));
            
            if(tab === 'browse') {
                document.querySelectorAll('.tab-btn')[0].classList.add('active');
                document.getElementById('browse-section').classList.add('active');
            } else {
                document.querySelectorAll('.tab-btn')[1].classList.add('active');
                document.getElementById('manage-section').classList.add('active');
            }
        }

        // ⚡ 核心功能 2：呼叫外部 API 自動填入資料
        async function fetchRecipeFromAPI() {
            const query = document.getElementById('api-query').value.trim();
            if(!query) {
                alert('請輸入料理的英文名稱（例如: Tacos, Sushi, Lasagna）');
                return;
            }

            try {
                // 使用免費、免密鑰的 TheMealDB API
                const response = await fetch(`https://www.themealdb.com/api/json/v1/1/search.php?s=${encodeURIComponent(query)}`);
                const data = await response.json();

                if(!data.meals) {
                    alert('在國際資料庫中找不到這道料理，請嘗試其他英文關鍵字！');
                    return;
                }

                const meal = data.meals[0];

                // 解析並整合 API 中散落的食材欄位 (strIngredient1 ~ 20)
                let ingredientsArray = [];
                for (let i = 1; i <= 20; i++) {
                    const ing = meal[`strIngredient${i}`];
                    const measure = meal[`strMeasure${i}`];
                    if (ing && ing.trim() !== "") {
                        ingredientsArray.push(`• ${ing} (${measure.trim()})`);
                    }
                }

                // 將抓到的國際資料自動填入表單中
                document.getElementById('form-name').value = meal.strMeal;
                document.getElementById('form-category').value = meal.strCategory;
                document.getElementById('form-area').value = meal.strArea;
                document.getElementById('form-ingredients').value = ingredientsArray.join('\n');
                document.getElementById('form-instructions').value = meal.strInstructions;
                document.getElementById('form-image').value = meal.strMealThumb;

                alert('🎉 API 資料成功自動填入！');
            } catch (error) {
                console.error(error);
                alert('呼叫 API 發生錯誤，請檢查網路。');
            }
        }

        // ⚡ 核心功能 3：透過 GAS 將表單資料存入 Google 試算表 (POST)
        async function submitToGoogleSheet(event) {
            event.preventDefault();
            
            if(GAS_URL.includes("請在此貼上")) {
                alert("請先將前端程式碼中的 GAS_URL 變數，替換成你部署完 Apps Script 的網頁應用程式網址！");
                return;
            }

            const newRecipe = {
                dishName: document.getElementById('form-name').value,
                category: document.getElementById('form-category').value,
                area: document.getElementById('form-area').value,
                ingredients: document.getElementById('form-ingredients').value,
                instructions: document.getElementById('form-instructions').value,
                imageUrl: document.getElementById('form-image').value
            };

            try {
                // 發送 POST 請求給 GAS 後端
                const response = await fetch(GAS_URL, {
                    method: 'POST',
                    mode: 'no-cors', // 使用 no-cors 模式避開跨網域限制
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(newRecipe)
                });

                alert('🚀 成功存入雲端 Google 試算表！網頁將重新載入。');
                document.getElementById('recipe-form').reset();
                location.reload(); // 重新載入，讓網頁同步雲端最新數據
            } catch (error) {
                console.error(error);
                alert('上傳至試算表失敗，請檢查 GAS 設定。');
            }
        }
    </script>
</body>
</html>
