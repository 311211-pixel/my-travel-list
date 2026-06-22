<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>美味探索家：國際料理食譜收藏卡</title>
  <style>
    body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background-color: #f4f4f9; text-align: center; padding: 20px; }
    .search-box { margin-bottom: 20px; }
    input[type="text"] { padding: 10px; width: 300px; border: 1px solid #ccc; border-radius: 5px; font-size: 16px; }
    button { padding: 10px 20px; font-size: 16px; cursor: pointer; background-color: #ff6b6b; color: white; border: none; border-radius: 5px; transition: 0.3s; }
    button:hover { background-color: #ff4757; }
    
    /* 3D 卡片翻轉效果 */
    .card-container { perspective: 1000px; width: 350px; height: 500px; margin: 0 auto; display: none; }
    .card { width: 100%; height: 100%; position: relative; transition: transform 0.8s; transform-style: preserve-3d; cursor: pointer; box-shadow: 0 4px 8px rgba(0,0,0,0.2); border-radius: 15px; }
    .card.is-flipped { transform: rotateY(180deg); }
    .card-face { width: 100%; height: 100%; position: absolute; backface-visibility: hidden; border-radius: 15px; overflow: hidden; background: white; display: flex; flex-direction: column; }
    
    /* 正面 (圖片與名稱) */
    .card-front img { width: 100%; height: 60%; object-fit: cover; }
    .card-front .info { padding: 20px; }
    .card-front h2 { margin: 10px 0; color: #2f3542; }
    .tag { display: inline-block; background: #dfe4ea; padding: 5px 10px; border-radius: 15px; font-size: 14px; color: #57606f; margin: 5px; }
    
    /* 背面 (食材與步驟) */
    .card-back { transform: rotateY(180deg); background: #fffdf5; padding: 20px; overflow-y: auto; text-align: left; }
    .card-back h3 { color: #ff6b6b; margin-top: 0; }
    .card-back p, .card-back ul { font-size: 14px; color: #2f3542; line-height: 1.5; }
    
    .action-btn { margin-top: 20px; display: none; }
  </style>
</head>
<body>

  <h1>🍳 美味探索家</h1>
  <p>輸入料理英文名稱（例如：Arrabiata, Sushi, Pancake），翻轉卡片查看食譜！</p>
  
  <div class="search-box">
    <input type="text" id="searchInput" placeholder="輸入料理英文名稱...">
    <button onclick="searchRecipe()">搜尋食譜</button>
  </div>

  <div class="card-container" id="cardContainer">
    <div class="card" id="recipeCard" onclick="flipCard()">
      <div class="card-face card-front">
        <img id="recipeImage" src="" alt="Recipe Image">
        <div class="info">
          <h2 id="recipeName">料理名稱</h2>
          <span class="tag" id="recipeCategory">類別</span>
          <span class="tag" id="recipeArea">風味</span>
          <p>👉 點擊卡片查看食譜</p>
        </div>
      </div>
      <div class="card-face card-back">
        <h3>🛒 主要食材</h3>
        <ul id="recipeIngredients"></ul>
        <h3>👩‍🍳 烹飪步驟</h3>
        <p id="recipeInstructions"></p>
      </div>
    </div>
  </div>

  <button class="action-btn" id="saveBtn" onclick="saveToGAS()">💾 收藏至 Google 試算表</button>

  <script>
    let currentRecipe = {}; // 暫存當前搜尋到的食譜資料

    // 翻轉卡片函數
    function flipCard() {
      document.getElementById('recipeCard').classList.toggle('is-flipped');
    }

    // 串接 TheMealDB API
    async function searchRecipe() {
      const query = document.getElementById('searchInput').value;
      if (!query) return alert("請輸入料理名稱！");

      const response = await fetch(`https://www.themealdb.com/api/json/v1/1/search.php?s=${query}`);
      const data = await response.json();

      if (data.meals) {
        const meal = data.meals[0]; // 取第一筆結果
        
        // 整理需要的資料
        currentRecipe = {
          name: meal.strMeal,
          category: meal.strCategory,
          area: meal.strArea,
          instructions: meal.strInstructions,
          imageUrl: meal.strMealThumb,
          ingredients: ''
        };

        // 整理食材清單 (API 設計為 strIngredient1 ~ 20)
        let ingredientsList = [];
        for (let i = 1; i <= 20; i++) {
          if (meal[`strIngredient${i}`]) {
            ingredientsList.push(`${meal[`strIngredient${i}`]} (${meal[`strMeasure${i}`]})`);
          }
        }
        currentRecipe.ingredients = ingredientsList.join(', ');

        // 更新網頁畫面
        document.getElementById('recipeImage').src = currentRecipe.imageUrl;
        document.getElementById('recipeName').innerText = currentRecipe.name;
        document.getElementById('recipeCategory').innerText = currentRecipe.category;
        document.getElementById('recipeArea').innerText = currentRecipe.area;
        document.getElementById('recipeInstructions').innerText = currentRecipe.instructions.substring(0, 300) + '...'; // 截斷過長步驟
        
        const ul = document.getElementById('recipeIngredients');
        ul.innerHTML = '';
        ingredientsList.slice(0, 5).forEach(ing => { // 只顯示前5個食材防版面爆掉
          let li = document.createElement('li');
          li.innerText = ing;
          ul.appendChild(li);
        });

        // 顯示卡片與儲存按鈕
        document.getElementById('cardContainer').style.display = 'block';
        document.getElementById('saveBtn').style.display = 'inline-block';
        
        // 確保搜尋新結果時卡片翻回正面
        document.getElementById('recipeCard').classList.remove('is-flipped');
      } else {
        alert("找不到此料理，請換個關鍵
