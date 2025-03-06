<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>pH Test Website (No ML Model with Advice)</title>
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
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
      background-color: #fff;
    }
    select, input {
      padding: 10px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }
    #subcategory-menu, #results, #ph-upload {
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
    <option value="ph-testing">pH Testing (Generic)</option>
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
    <pre id="result-text" style="white-space: pre-wrap; text-align: center;"></pre>
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
        addOption(subcategorySelection, "Drinking Water", "drinkable");
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

    /* --- Reference Ranges & Detailed Advice --- */
    const referenceRanges = {
      "urine": {
        min: 4.5,
        max: 8.0,
        normal: "Urine pH is within the normal range. No immediate concerns.",
        abnormal: "Urine pH is abnormal. It is advised that you consult a doctor for further evaluation."
      },
      "menstrual": {
        min: 6.0,
        max: 7.0,
        normal: "Menstrual blood pH is within the expected range.",
        abnormal: "Menstrual blood pH is abnormal. Please consult a healthcare provider."
      },
      "shampoo": {
        min: 4.5,
        max: 7.5,
        normal: "Shampoo pH is within the typical range.",
        abnormal: "Shampoo pH is abnormal. Consider testing another product or contacting the manufacturer."
      },
      "soap": {
        min: 9.0,
        max: 11.0,
        normal: "Soap pH is as expected.",
        abnormal: "Soap pH is abnormal. This may affect skin health; consider using an alternative."
      },
      "cream": {
        min: 5.0,
        max: 7.5,
        normal: "Skin creams/lotions pH is normal.",
        abnormal: "Skin creams/lotions pH is abnormal. If irritation occurs, consult a dermatologist."
      },
      "facewash": {
        min: 5.0,
        max: 7.5,
        normal: "Face wash pH is within the normal range.",
        abnormal: "Face wash pH is abnormal. Consider switching products."
      },
      "toothpaste": {
        min: 6.5,
        max: 8.5,
        normal: "Toothpaste pH is normal.",
        abnormal: "Toothpaste pH is abnormal. If experiencing sensitivity, consult your dentist."
      },
      "food": {
        min: 3.0,
        max: 4.5,
        normal: "Food items pH is within the expected acidic range.",
        abnormal: "Food items pH is abnormal. Do not consume the product and consider contacting the vendor."
      },
      "curd": {
        min: 4.0,
        max: 4.5,
        normal: "Curd pH is within expected range.",
        abnormal: "Curd pH is abnormal. Discard the product immediately."
      },
      "milk": {
        min: 6.7,
        max: 6.9,
        normal: "Milk pH is normal.",
        abnormal: "Milk pH is abnormal. Avoid consuming and consider returning the product."
      },
      "wine": {
        min: 3.0,
        max: 3.8,
        normal: "Wine pH is within the expected range.",
        abnormal: "Wine pH is abnormal. Discard the product and consider quality control measures."
      },
      "detergents": {
        min: 10.0,
        max: 12.0,
        normal: "Detergents pH is typical for cleaning agents.",
        abnormal: "Detergents pH is abnormal. Review product quality or consult the manufacturer."
      },
      "aquarium": {
        min: 6.5,
        max: 8.0,
        normal: "Aquarium water pH is normal.",
        abnormal: "Aquarium water pH is abnormal. Adjust with a pH increaser or decreaser as per guidelines."
      },
      "drinkable": {
        min: 6.5,
        max: 8.5,
        normal: "Drinking water pH is within the acceptable range.",
        abnormal: "Drinking water pH is abnormal. If highly acidic or alkaline, consider treating with a pH neutralizer or discarding the water and using an alternative source."
      },
      "swimmingpool": {
        min: 7.2,
        max: 7.8,
        normal: "Swimming pool water pH is ideal.",
        abnormal: "Swimming pool water pH is abnormal. Adjust chemicals carefully (typically by adding specific doses of pH increaser or decreaser) according to manufacturer guidelines."
      },
      "soil": {
        min: 5.5,
        max: 7.5,
        normal: "Soil pH is within the normal range.",
        abnormal: "Soil pH is abnormal. If the soil is too acidic, you might add garden lime (about 50–100 grams per square meter). If it is too alkaline, consider applying elemental sulfur. Consult your local agricultural extension service for precise recommendations."
      },
      "ricesoil": {
        min: 5.0,
        max: 7.0,
        normal: "Rice soil pH is within the typical range.",
        abnormal: "Rice soil pH is abnormal. Amend the soil as per local agronomic guidelines; consult an agronomist for accurate dosing."
      },
      "wheatsoil": {
        min: 6.0,
        max: 7.5,
        normal: "Wheat soil pH is ideal.",
        abnormal: "Wheat soil pH is abnormal. Adjust with lime or sulfur as recommended by local agricultural advisories."
      }
    };

    // Function to generate interpretation/advice based on the test selection and estimated pH.
    function getInterpretation(testSelection, estimatedPH) {
      if (testSelection === "ph-testing") {
        return "This is a generic pH test result. If the pH appears abnormal, consider further testing.";
      }
      var subSelect = document.getElementById("subcategory-selection");
      var subcategory = subSelect ? subSelect.value : "";
      if (!subcategory) return "No specific subcategory selected. Please choose one for detailed interpretation.";
      let range = referenceRanges[subcategory];
      if (range) {
        if (estimatedPH < range.min || estimatedPH > range.max) {
          return range.abnormal;
        } else {
          return range.normal;
        }
      }
      return "No interpretation available for the selected test.";
    }

    /* --- pH Color Mapping & Image Processing Functions --- */
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
      function f(t) { return t > 0.008856 ? Math.pow(t, 1/3) : (7.787*t)+(16/116); }
      let L = (Y/Yn > 0.008856) ? (116 * Math.pow(Y/Yn, 1/3))-16 : 903.3*(Y/Yn);
      let a = 500 * (f(X/Xn) - f(Y/Yn));
      let bVal = 200 * (f(Y/Yn) - f(Z/Yn));
      return { L: L, a: a, b: bVal };
    }

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

    function autoWhiteBalance(data) {
      let sumR = 0, sumG = 0, sumB = 0;
      const pixelCount = data.length / 4;
      for (let i = 0; i < data.length; i += 4) {
        sumR += data[i];
        sumG += data[i+1];
        sumB += data[i+2];
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
        data[i+1] = Math.min(data[i+1] * scaleG, 255);
        data[i+2] = Math.min(data[i+2] * scaleB, 255);
      }
      return data;
    }

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
          const canvas = document.createElement("canvas");
          canvas.width = img.width;
          canvas.height = img.height;
          const ctx = canvas.getContext("2d");
          ctx.drawImage(img, 0, 0);
          let imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
          imageData.data = autoWhiteBalance(imageData.data);
          ctx.putImageData(imageData, 0, 0);
          let sumR = 0, sumG = 0, sumB = 0;
          const totalPixels = imageData.data.length / 4;
          for (let i = 0; i < imageData.data.length; i += 4) {
            sumR += imageData.data[i];
            sumG += imageData.data[i+1];
            sumB += imageData.data[i+2];
          }
          let avgR = sumR / totalPixels;
          let avgG = sumG / totalPixels;
          let avgB = sumB / totalPixels;
          let labValue = rgbToLab(avgR, avgG, avgB);
          let estimatedPH = matchToPH(avgR, avgG, avgB);
          let testSelection = document.getElementById("test-selection").value;
          let interpretation = getInterpretation(testSelection, estimatedPH);
          let resultString = "Detailed Results:\n" +
            "Average RGB: (" + avgR.toFixed(2) + ", " + avgG.toFixed(2) + ", " + avgB.toFixed(2) + ")\n" +
            "Converted LAB: (L: " + labValue.L.toFixed(2) + ", a: " + labValue.a.toFixed(2) + ", b: " + labValue.b.toFixed(2) + ")\n" +
            "Estimated pH: " + estimatedPH + "\n\n" +
            "Interpretation & Advice: " + interpretation + "\n\n" +
            "Disclaimer: This information is for guidance only and does not replace professional advice.";
          document.getElementById("result-text").innerText = resultString;
          document.getElementById("results").style.display = "block";
        };
      };
      reader.readAsDataURL(file);
    }

function downloadResults() {
  // Log that the function has been triggered.
  console.log("downloadResults() function called.");
  
  // Check if the jsPDF object is available.
  if (!window.jspdf || !window.jspdf.jsPDF) {
    console.error("jsPDF library is not loaded.");
    alert("Error: jsPDF library is not loaded. Please check your CDN link.");
    return;
  }
  
  // Create a new jsPDF instance.
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF({
    orientation: 'portrait',
    unit: 'mm',
    format: 'a4'
  });
  
  const pageWidth = doc.internal.pageSize.getWidth();
  const margin = 15;
  
  // Header with a decorative background.
  doc.setFillColor(0, 123, 255);
  doc.rect(0, 0, pageWidth, 30, 'F');
  doc.setFont("helvetica", "bold");
  doc.setFontSize(22);
  doc.setTextColor(255, 255, 255);
  doc.text("pH Test Report", pageWidth / 2, 20, { align: 'center' });
  
  // Draw a divider below header.
  doc.setDrawColor(0, 0, 0);
  doc.setLineWidth(0.5);
  doc.line(margin, 33, pageWidth - margin, 33);
  
  // Retrieve the result text.
  const resultTextElem = document.getElementById("result-text");
  if (!resultTextElem) {
    console.error("Result text element not found.");
    alert("Error: Result text element not found.");
    return;
  }
  
  const resultText = resultTextElem.innerText;
  if (!resultText) {
    console.warn("No result text found. The report might be empty.");
    alert("Warning: There is no result available to download.");
    return;
  }
  
  // Split text into lines that fit within the page width.
  const textLines = doc.splitTextToSize(resultText, pageWidth - 2 * margin);
  doc.setFont("helvetica", "normal");
  doc.setFontSize(12);
  doc.setTextColor(0, 0, 0);
  doc.text(textLines, margin, 45);

  // Decorative border around the page.
  doc.setDrawColor(0, 123, 255);
  doc.setLineWidth(2);
  doc.rect(5, 5, pageWidth - 10, doc.internal.pageSize.getHeight() - 10);
  
  // Add a footer.
  doc.setFont("helvetica", "italic");
  doc.setFontSize(10);
  doc.setTextColor(100);
  doc.text("Thank you for using our pH testing service", 
           pageWidth / 2, doc.internal.pageSize.getHeight() - 10, { align: 'center' });
  
  console.log("PDF generation complete, saving file.");
  // Save the generated PDF.
  doc.save("pH_Test_Results.pdf");
}

  </script>
</body>
</html>
