<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>pH Test Website (No ML Model)</title>
    <!-- Include jsPDF library for PDF downloads -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
      body {
        font-family: 'Open Sans', Arial, sans-serif;
        margin: 20px;
        background: linear-gradient(135deg, #e2e2e2, #c9c9c9);
        color: #333;
        transition: background-color 0.5s, color 0.5s;
      }
      h1, h2 {
        text-align: center;
      }
      h1 {
        color: #4caf50;
        animation: fadeIn 2s ease-in-out;
        font-weight: 700;
        text-shadow: 2px 2px #e2e2e2;
      }
      label, select, input, button {
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
      #subcategory-menu,
      #results,
      #ph-upload {
        display: none;
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
    
    <!-- Test Type Selection -->
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
    
    <!-- Subcategory Menu (dynamically populated) -->
    <div id="subcategory-menu">
      <label for="subcategory-selection">Select a Subcategory:</label>
      <select id="subcategory-selection"></select>
    </div>
    
    <!-- pH Strip Upload -->
    <div id="ph-upload">
      <label for="file-upload">Upload pH Strip Image:</label>
      <input type="file" id="file-upload" accept="image/*" onchange="analyzeImage()" />
    </div>
    
    <!-- Test Results Section -->
    <div id="results">
      <h2>Test Results</h2>
      <p id="result-text"></p>
      <button id="download-btn" onclick="downloadResults()">Download Results</button>
    </div>
    
    <script>
      /* --- Functions for Subcategory Options --- */
      function showSubcategories() {
        var selection = document.getElementById("test-selection").value;
        var subcategoryMenu = document.getElementById("subcategory-menu");
        var subcategorySelection = document.getElementById("subcategory-selection");
        subcategorySelection.innerHTML = "";
        if (
          selection === "health" ||
          selection === "hygiene" ||
          selection === "food" ||
          selection === "household" ||
          selection === "water" ||
          selection === "soil"
        ) {
          subcategoryMenu.style.display = "block";
          document.getElementById("ph-upload").style.display = "block";
          addOptionsForSelection(selection, subcategorySelection);
        } else if (selection === "ph-testing") {
          document.getElementById("ph-upload").style.display = "block";
          subcategoryMenu.style.display = "none";
        } else {
          subcategoryMenu.style.display = "none";
          document.getElementById("ph-upload").style.display = "none";
        }
      }

      function addOptionsForSelection(selection, subcategorySelection) {
        if (selection === "health") {
          addOption(subcategorySelection, "Urine (Healthy)", "urine");
          addOption(subcategorySelection, "Menstrual Blood", "menstrual");
        } else if (selection === "hygiene") {
          addOption(subcategorySelection, "Shampoo", "shampoo");
          addOption(subcategorySelection, "Soap", "soap");
          addOption(subcategorySelection, "Skin Creams/Lotions", "cream");
          addOption(subcategorySelection, "Face Wash", "facewash");
          addOption(subcategorySelection, "Toothpaste", "toothpaste");
        } else if (selection === "food") {
          addOption(subcategorySelection, "Food Items", "food");
          addOption(subcategorySelection, "Curd Formation", "curd");
          addOption(subcategorySelection, "Milk", "milk");
          addOption(subcategorySelection, "Wine", "wine");
        } else if (selection === "household") {
          addOption(subcategorySelection, "Detergents", "detergents");
          addOption(subcategorySelection, "Aquarium Water", "aquarium");
        } else if (selection === "water") {
          addOption(subcategorySelection, "Water (Drinkable)", "drinkable");
          addOption(subcategorySelection, "Swimming Pool", "swimmingpool");
        } else if (selection === "soil") {
          addOption(subcategorySelection, "Soil", "soil");
          addOption(subcategorySelection, "Rice Soil", "ricesoil");
          addOption(subcategorySelection, "Wheat Soil", "wheatsoil");
        }
      }

      function addOption(select, text, value) {
        var option = document.createElement("option");
        option.text = text;
        option.value = value;
        select.add(option);
      }

      /* --- pH Color Mapping & Image Processing Functions --- */
      // Reference mapping: each object maps an average RGB to a pH value.
      const colorPHMap = [
        { r: 185, g: 92,  b: 96,  pH: 1 },
        { r: 192, g: 110, b: 121, pH: 2 },
        { r: 190, g: 132, b: 153, pH: 3 },
        { r: 180, g: 140, b: 168, pH: 4 },
        { r: 176, g: 145, b: 173, pH: 5 },
        { r: 172, g: 147, b: 177, pH: 6 },
        { r: 168, g: 149, b: 180, pH: 7 },
        { r: 165, g: 146, b: 181, pH: 8 },
        { r: 167, g: 148, b: 182, pH: 9 },
        { r: 162, g: 146, b: 180, pH: 10 },
        { r: 153, g: 151, b: 177, pH: 11 },
        { r: 164, g: 155, b: 169, pH: 12 },
        { r: 145, g: 106, b: 129, pH: 13 },
        { r: 99,  g: 85,  b: 101, pH: 14 }
      ];

      // Convert from RGB to LAB; LAB is more perceptually uniform for color difference.
      function rgbToLab(r, g, b) {
        let R = r / 255, G = g / 255, B = b / 255;
        R = (R > 0.04045) ? Math.pow((R + 0.055) / 1.055, 2.4) : R / 12.92;
        G = (G > 0.04045) ? Math.pow((G + 0.055) / 1.055, 2.4) : G / 12.92;
        B = (B > 0.04045) ? Math.pow((B + 0.055) / 1.055, 2.4) : B / 12.92;
        let X = R * 0.4124 + G * 0.3576 + B * 0.1805;
        let Y = R * 0.2126 + G * 0.7152 + B * 0.0722;
        let Z = R * 0.0193 + G * 0.1192 + B * 0.9505;
        X *= 100; Y *= 100; Z *= 100;
        const Xn = 95.047, Yn = 100.0, Zn = 108.883;
        function f(t) { return t > 0.008856 ? Math.pow(t, 1/3) : (7.787 * t) + (16/116); }
        let L = (Y/Yn > 0.008856) ? (116 * Math.pow(Y/Yn, 1/3))-16 : 903.3 * (Y/Yn);
        let a = 500 * (f(X/Xn) - f(Y/Yn));
        let bVal = 200 * (f(Y/Yn) - f(Z/Yn));
        return { L: L, a: a, b: bVal };
      }

      // Compare average LAB color with reference values and return the closest pH.
      function matchToPH(r, g, b) {
        const labColor = rgbToLab(r, g, b);
        let closestMatch = null;
        let minDeltaE = Infinity;
        colorPHMap.forEach(ref => {
          const refLab = rgbToLab(ref.r, ref.g, ref.b);
          const deltaE = Math.sqrt(
            Math.pow(labColor.L - refLab.L, 2) +
            Math.pow(labColor.a - refLab.a, 2) +
            Math.pow(labColor.b - refLab.b, 2)
          );
          if (deltaE < minDeltaE) {
            minDeltaE = deltaE;
            closestMatch = ref.pH;
          }
        });
        return closestMatch || "Unknown";
      }

      // Auto white balance adjustment using the Gray World Assumption.
      function autoWhiteBalance(data) {
        let sumR = 0, sumG = 0, sumB = 0;
        const pixelCount = data.length / 4;
        for (let i = 0; i < data.length; i += 4) {
          sumR += data[i];
          sumG += data[i + 1];
          sumB += data[i + 2];
        }
        const avgR = sumR / pixelCount;
        const avgG = sumG / pixelCount;
        const avgB = sumB / pixelCount;
        const avgGray = (avgR + avgG + avgB) / 3;
        const scaleR = avgGray / avgR;
        const scaleG = avgGray / avgG;
        const scaleB = avgGray / avgB;
        for (let i = 0; i < data.length; i += 4) {
          data[i] = Math.min(data[i] * scaleR, 255);
          data[i + 1] = Math.min(data[i + 1] * scaleG, 255);
          data[i + 2] = Math.min(data[i + 2] * scaleB, 255);
        }
        return data;
      }

      // Process the uploaded image: read it, perform auto white balance, compute the average color, and estimate the pH.
      async function analyzeImage() {
        const fileInput = document.getElementById("file-upload");
        const file = fileInput.files[0];
        if (!file) {
          alert("Please upload an image first!");
          return;
        }
        const reader = new FileReader();
        reader.onloadend = function () {
          const dataURL = reader.result;
          let img = new Image();
          img.src = dataURL;
          img.onload = function () {
            // Create a canvas and draw the image on it.
            const canvas = document.createElement("canvas");
            canvas.width = img.width;
            canvas.height = img.height;
            const ctx = canvas.getContext("2d");
            ctx.drawImage(img, 0, 0);
            let imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            imageData.data = autoWhiteBalance(imageData.data);
            ctx.putImageData(imageData, 0, 0);
            // Calculate average RGB.
            let sumR = 0, sumG = 0, sumB = 0;
            const totalPixels = imageData.data.length / 4;
            for (let i = 0; i < imageData.data.length; i += 4) {
              sumR += imageData.data[i];
              sumG += imageData.data[i + 1];
              sumB += imageData.data[i + 2];
            }
            let avgR = sumR / totalPixels;
            let avgG = sumG / totalPixels;
            let avgB = sumB / totalPixels;
            // Estimate the pH value.
            let estimatedPH = matchToPH(avgR, avgG, avgB);
            document.getElementById("result-text").innerText = "Estimated pH: " + estimatedPH;
            document.getElementById("results").style.display = "block";
          };
        };
        reader.readAsDataURL(file);
      }

      // Generate a PDF of the test results using jsPDF.
      function downloadResults() {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF();
        const resultText = document.getElementById("result-text").innerText;
        doc.text("pH Test Results", 10, 10);
        doc.text(resultText, 10, 20);
        doc.save("pH_Test_Results.pdf");
      }
    </script>
  </body>
</html>
