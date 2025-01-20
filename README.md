# Testing-of-pH
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>pH Test Website</title>
    <style>
    body {
        font-family: 'Open Sans', Arial, sans-serif;
        margin: 20px;
        background: linear-gradient(135deg, #e2e2e2, #c9c9c9);
        color: #333;
        transition: background-color 0.5s, color 0.5s;
    }
    h1 {
        color: #4CAF50;
        text-align: center;
        animation: fadeIn 2s ease-in-out;
        font-weight: 700;
        text-shadow: 2px 2px #e2e2e2;
    }
    label, select, input {
        display: block;
        margin: 20px auto;
        animation: slideIn 1s ease-in-out;
        font-size: 1.1em;
        width: 80%;
        max-width: 300px;
        text-align: center;
        padding: 10px;
        border-radius: 10px;
        box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
        background-color: #fff;
    }
    select, input {
        padding: 10px;
        border-radius: 5px;
        border: 1px solid #ccc;
    }
    #subcategory-menu, #ph-upload, #results {
        display: none;
    }
    #ph-image {
        margin-top: 10px;
        animation: scaleUp 1s ease-in-out;
    }
    #download-btn {
        margin-top: 20px;
        background-color: #4CAF50;
        color: white;
        border: none;
        padding: 15px 30px;
        cursor: pointer;
        border-radius: 5px;
        transition: background-color 0.3s, transform 0.3s;
        font-size: 1.1em;
        width: 80%;
        max-width: 300px;
        text-align: center;
        box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
    }
    #download-btn:hover {
        background-color: #45a049;
        transform: scale(1.1);
    }
    @keyframes fadeIn {
        0% { opacity: 0; }
        100% { opacity: 1; }
    }
    @keyframes slideIn {
        0% { transform: translateX(-100%); }
        100% { transform: translateX(0); }
    }
    @keyframes scaleUp {
        0% { transform: scale(0); }
        100% { transform: scale(1); }
    }
    .decorative-element {
        position: absolute;
        background: radial-gradient(circle at center, #fffc00, #ff0084);
        border-radius: 50%;
        opacity: 0.4;
        animation: float 4s ease-in-out infinite;
    }
    @keyframes float {
        0% { transform: translateY(0); }
        50% { transform: translateY(-20px); }
        100% { transform: translateY(0); }
    }
</style>



</head>
<body>
    <h1>Welcome to the pH Test Website</h1>
    <!-- Decorative Elements -->
    <div class="decorative-element" style="top: 20px; left: 20px; width: 100px; height: 100px;"></div>
    <div class="decorative-element" style="top: 50%; left: 80%; width: 150px; height: 150px;"></div>
    <div class="decorative-element" style="bottom: 10%; right: 10%; width: 200px; height: 200px;"></div>

   <!-- Initial Menu -->
    <label for="test-selection">Select a Test:</label>
    <select id="test-selection" onchange="showSubcategories()">
        <option value="">--Select--</option>
        <option value="health">Health</option>
        <option value="hygiene">Hygiene</option>
        <option value="food">Food Items</option>
        <option value="household">Household Utilities</option>
        <option value="water">Water Quality</option>
        <option value="soil">Soil</option>
        <option value="ph-testing">pH Testing</option>
    </select>

    
    <!-- Second Menu -->
    <div id="subcategory-menu">
        <label for="subcategory-selection">Select a Subcategory:</label>
        <select id="subcategory-selection">
            <!-- Options will be dynamically populated based on initial selection -->
        </select>
    </div>
    <!-- pH Strip Upload -->
    <div id="ph-upload">
        <label for="file-upload">Upload pH Strip Image:</label>
        <input type="file" id="file-upload" accept="image/*" onchange="analyzeImage()">
    </div>
    
    <!-- Results Section -->
    <div id="results">
        <h2>Test Results</h2>
        <p id="result-text"></p>
        <button id="download-btn" onclick="downloadResults()">Download Results</button>
    </div>
    <script>
        function showSubcategories() {
            var selection = document.getElementById('test-selection').value;
            var subcategoryMenu = document.getElementById('subcategory-menu');
            var subcategorySelection = document.getElementById('subcategory-selection');
            
            // Clear previous options
            subcategorySelection.innerHTML = '';

            if (selection === 'health' || selection === 'hygiene' || selection === 'food' || selection === 'household' || selection === 'water' || selection === 'soil') {
                subcategoryMenu.style.display = 'block';
                document.getElementById('ph-upload').style.display = 'block';
                addOptionsForSelection(selection, subcategorySelection);
            } else if (selection === 'ph-testing') {
                document.getElementById('ph-upload').style.display = 'block';
                subcategoryMenu.style.display = 'none';
            } else {
                subcategoryMenu.style.display = 'none';
                document.getElementById('ph-upload').style.display = 'none';
            }
        }
        function addOptionsForSelection(selection, subcategorySelection) {
            if (selection === 'health') {
                addOption(subcategorySelection, 'Urine (Healthy)', 'urine');
                addOption(subcategorySelection, 'Menstrual Blood', 'menstrual');
            } else if (selection === 'hygiene') {
                addOption(subcategorySelection, 'Shampoo', 'shampoo');
                addOption(subcategorySelection, 'Soap', 'soap');
                addOption(subcategorySelection, 'Skin Creams/Lotions', 'cream');
                addOption(subcategorySelection, 'Face Wash', 'facewash');
                addOption(subcategorySelection, 'Toothpaste', 'toothpaste');
            } else if (selection === 'food') {
                addOption(subcategorySelection, 'Food Items', 'food');
                addOption(subcategorySelection, 'Curd Formation', 'curd');
                addOption(subcategorySelection, 'Milk', 'milk');
                addOption(subcategorySelection, 'Wine', 'wine');
            } else if (selection === 'household') {
                addOption(subcategorySelection, 'Detergents', 'detergents');
                addOption(subcategorySelection, 'Aquarium Water', 'aquarium');
            } else if (selection === 'water') {
                addOption(subcategorySelection, 'Water (Drinkable)', 'drinkable');
                addOption(subcategorySelection, 'Swimming Pool', 'swimmingpool');
            } else if (selection === 'soil') {
                addOption(subcategorySelection, 'Soil', 'soil');
                addOption(subcategorySelection, 'Rice Soil', 'ricesoil');
                addOption(subcategorySelection, 'Wheat Soil', 'wheatsoil');
            }
        }

        function addOption(select, text, value) {
            var option = document.createElement('option');
            option.text = text;
            option.value = value;
            select.add(option);
        }
        function detectPH(imageData) {
            return new Promise((resolve) => {
                const img = new Image();
                img.src = imageData;

                img.onload = () => {
                    // Draw image on canvas for analysis
                    const canvas = document.createElement("canvas");
                    const ctx = canvas.getContext("2d");
                    canvas.width = img.width;
                    canvas.height = img.height;
                    ctx.drawImage(img, 0, 0);

                    // Get image data (pixels)
                    const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
                    const { data } = imageData;

                    // Calculate average color of the image
                    let r = 0, g = 0, b = 0;
                    for (let i = 0; i < data.length; i += 4) {
                        r += data[i];     // Red
                        g += data[i + 1]; // Green
                        b += data[i + 2]; // Blue
                    }
                    const pixelCount = data.length / 4;
                    r = Math.floor(r / pixelCount);
                    g = Math.floor(g / pixelCount);
                    b = Math.floor(b / pixelCount);

                    // Match average color to pH scale
                    const detectedPH = matchToPH(r, g, b);
                    resolve(detectedPH);
                };
            });
        }
        function matchToPH(r, g, b) {
            const colorPHMap = [
                { r: 255, g: 0, b: 0, pH: 1 },    // Red for pH 1
                { r: 255, g: 69, b: 0, pH: 2 },   // Red-Orange for pH 2
                { r: 255, g: 165, b: 0, pH: 3 },  // Orange for pH 3
                { r: 255, g: 215, b: 0, pH: 4 },  // Yellow-Orange for pH 4
                { r: 255, g: 255, b: 0, pH: 5 },  // Yellow for pH 5
                { r: 173, g: 255, b: 47, pH: 6 }, // Yellow-Green for pH 6
                { r: 0, g: 128, b: 0, pH: 7 },    // Green for pH 7
                { r: 0, g: 255, b: 127, pH: 8 },  // Green-Blue for pH 8
                { r: 0, g: 0, b: 255, pH: 9 },    // Blue for pH 9
                { r: 75, g: 0, b: 130, pH: 10 },  // Indigo for pH 10
                { r: 238, g: 130, b: 238, pH: 11 } // Violet for pH 11
            ];

            // Find the closest match to the average color
            let closestMatch = null;
            let minDistance = Infinity;
                        colorPHMap.forEach(({ r: cr, g: cg, b: cb, pH }) => {
                const distance = Math.sqrt((r - cr) ** 2 + (g - cg) ** 2 + (b - cb) ** 2);
                if (distance < minDistance) {
                    minDistance = distance;
                    closestMatch = pH;
                }
            });

            return closestMatch || "Unknown";
        }

        async function analyzeImage() {
            const upload = document.getElementById('file-upload');
            if (!upload.files.length) {
                alert('Please upload an image first.');
                return;
            }

            const img = upload.files[0];
            const fileReader = new FileReader();
            fileReader.onload = async () => {
                const detectedPH = await detectPH(fileReader.result);
                displayResults(detectedPH);
            };
            fileReader.readAsDataURL(img);
        }
        function displayResults(detectedPH) {
            document.getElementById('results').style.display = 'block';
            document.getElementById('result-text').innerText = 'pH Level: ' + detectedPH;

            // Customize result interpretation based on test type
            var testType = document.getElementById('test-selection').value;
            var subCategory = document.getElementById('subcategory-selection').value;

            if (testType === 'health') {
                if (subCategory === 'urine') {
                    if (detectedPH < 4.5 || detectedPH > 8) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Indicates urinary tract infections, kidney issues, dehydration.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Healthy pH Level.';
                    }
                } else if (subCategory === 'menstrual') {
                    if (detectedPH > 4.5) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Indicates bacterial infections like bacterial vaginosis.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Healthy pH Level.';
                    }
                }
            } else if (testType === 'hygiene') {
                if (subCategory === 'shampoo') {
                    if (detectedPH > 6 || detectedPH < 4) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Can cause scalp irritation, dryness, or hair damage.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level.';
                    }
                } else if (subCategory === 'soap') {
                    if (detectedPH > 10) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Can cause excessive dryness, skin irritation.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level.';
                    }
                } else if (subCategory === 'cream') {
                    if (detectedPH > 6) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Can disturb the skin barrier, leading to acne or rashes.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for skin creams/lotions.';
                    }
                } else if (subCategory === 'facewash') {
                    if (detectedPH > 6) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Disrupts skin barrier, increases sensitivity.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for face wash.';
                    }
                } else if (subCategory === 'toothpaste') {
                    if (detectedPH < 6.5 || detectedPH > 7.5) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Acidic toothpaste erodes enamel or causes oral irritation.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for toothpaste.';
                    }
                }
            } else if (testType === 'food') {
                if (subCategory === 'food') {
                    if (detectedPH < 4 || detectedPH > 7) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Highly acidic foods cause dental erosion or indicate spoilage.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Normal pH Level.';
                    }
                } else if (subCategory === 'curd') {
                    if (detectedPH > 4.6) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Indicates incomplete fermentation or spoilage.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for curd formation.';
                    }
                } else if (subCategory === 'milk') {
                    if (detectedPH < 6.4 || detectedPH > 6.8) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Indicates spoilage or bacterial contamination.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for milk.';
                    }
                } else if (subCategory === 'wine') {
                    if (detectedPH < 3 || detectedPH > 4) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Risk of microbial spoilage or overly tart taste.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for wine.';
                    }
                }
            } else if (testType === 'household') {
                if (subCategory === 'detergents') {
                    if (detectedPH > 12) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Can harm skin, damages delicate clothing.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level.';
                    }
                } else if (subCategory === 'aquarium') {
                    if (detectedPH < 6.5 || detectedPH > 8.5) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Stresses fish, harms gills, or promotes algae overgrowth.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level.';
                    }
                }
            } else if (testType === 'water') {
                if (subCategory === 'drinkable') {
                    if (detectedPH < 6.5 || detectedPH > 8) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Water is corrosive, unsafe, or has a bitter taste and scale buildup.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Safe drinking water pH Level.';
                    }
                } else if (subCategory === 'swimmingpool') {
                    if (detectedPH < 7.2 || detectedPH > 7.8) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Can irritate skin and eyes, cause cloudy water or skin irritation.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for swimming pool.';
                    }
                }
            } else if (testType === 'soil') {
                if (subCategory === 'soil') {
                    if (detectedPH < 5.5 || detectedPH > 7) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Too acidic or too alkaline, limits growth of certain plants.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for general soil.';
                    }
                } else if (subCategory === 'ricesoil') {
                    if (detectedPH < 5.5 || detectedPH > 6.5) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Acidic soil stunts growth or alkaline soil affects yield.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for rice soil.';
                    }
                                } else if (subCategory === 'wheatsoil') {
                    if (detectedPH < 6 || detectedPH > 7.5) {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Abnormal pH. Limits nutrient uptake or reduces nitrogen efficiency.';
                    } else {
                        document.getElementById('result-text').innerText += '\nResult Interpretation: Ideal pH Level for wheat soil.';
                    }
                }
            }
        }
        
        function downloadResults() {
            var resultText = document.getElementById('result-text').innerText;
            var blob = new Blob([resultText], { type: 'text/plain' });
            var link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = 'test-results.txt';
            link.click();
        }
    </script>
</body>
</html>
