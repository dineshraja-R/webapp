<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Plant Disease Detection</title>
    <link rel="stylesheet" href="./index.css">
    <Style>
        #result{
            background-color: wheat;
        }
    </Style>
</head>
<body>
    <header>
        <h1>Predicting the Frequent Diseases in the Plants Using Image Processing</h1>
        <h2>Live in lab project</h2>
    </header>
    <div id="videoContainer">
        <video id="videoElement" autoplay></video>

    </div>
 
    <img src="" alt="" id="capturedImage" >
   
    <centre><h1 style="color: black;" id="result"></h1></centre>

    <table>
        <tr>
           <td>    
              <div class="container"> 
                  <h2>TOMATO </h2>
                  <img width="200px" src="https://cdn.britannica.com/16/187216-131-FB186228/tomatoes-tomato-plant-Fruit-vegetable.jpg" alt="">
                  <input type="file" id="tomatoFileInput" accept="image/*">
                  <button onclick="openCamera()">OPEN CAMERA</button>
                  <button onclick="captureAndPredict('tomato')">predict</button>
              </div>
          </td>
          <td>
              <div class="container"> 
                  <h2>POTATO</h2>
                  <img width="200px" src="https://m.media-amazon.com/images/I/51lTK6iktYL._AC_UF1000,1000_QL80_.jpg" alt="">
                  <input type="file" id="potatoFileInput" accept="image/*">
                  <button onclick="openCamera()">OPEN CAMERA</button>
                  <button onclick="captureAndPredict('potato')">predict</button>
              </div>
          </td>
        </tr>
        <tr> 
           <td>
               <div class="container"> 
                  <h2>BANANA</h2>
                  <img width="200px" src="https://cdn4.volusion.store/uyqbk-sezkn/v/vspfiles/photos/FRUBAN-FRU-S-TX-STAR-2.jpg?v-cache=1686030121" alt="">
                  <input type="file" id="bananaFileInput" accept="image/*">
                  <button onclick="openCamera()">OPEN CAMERA</button>
                  <button onclick="captureAndPredict('banana')">predict</button>
              </div>
          </td>
          <td>
              <div class="container"> 
                  <h2>CORN</h2>
                  <img width="200px" src="https://bonnieplants.com/cdn/shop/articles/BONNIE-PLANTS_corn-iStock-857670630-2400px_1ce4b5df-20b7-433c-acde-8703235c66be.jpg?v=1642542000&width=1000" alt="">
                  <input type="file" id="cornFileInput" accept="image/*">
                  <button onclick="openCamera()">OPEN CAMERA</button>
                  <button onclick="captureAndPredict('corn')">predict</button>
              </div>
          </td>
        </tr>
    </table>
  
    <script>
        let videoStream;
        const videoElement = document.getElementById('videoElement');
        const capturedImageElement = document.getElementById('capturedImage');

        async function openCamera() {
            try {
                videoStream = await navigator.mediaDevices.getUserMedia({ video: true });
                videoElement.srcObject = videoStream;
            } catch (error) {
                console.error('Error accessing camera:', error);
            }
        }

        async function captureAndPredict(plant_name) {
            let blob;
            const fileInput = document.getElementById(`${plant_name}FileInput`);
            if (fileInput.files.length > 0) {
             
                blob = fileInput.files[0];
                console.log("file selected");
            } else {
                // If no file selected, capture image from camera
                if (!videoStream) {
                    console.error('No camera stream available.');
                    return;
                }

                const canvas = document.createElement('canvas');
                const context = canvas.getContext('2d');
                canvas.width = videoElement.videoWidth;
                canvas.height = videoElement.videoHeight;
                context.drawImage(videoElement, 0, 0, canvas.width, canvas.height);

                blob = await new Promise(resolve => canvas.toBlob(resolve, 'image/jpeg'));
                capturedImageElement.style.width = "200px";
                capturedImageElement.style.height = "200px";
                capturedImageElement.src = URL.createObjectURL(blob);
                videoStream.getTracks().forEach(track => track.stop());
            }

            const formData = new FormData();
            formData.append('file', blob);

            try {
                const response = await fetch(`http://localhost:1000/predict/${plant_name}`, {
                    method: 'POST',
                    body: formData
                });

                const result = await response.json();
                document.getElementById('result').textContent = `Predicted Disease: ${result.class}`;
                console.log(result.class);
            } catch (error) {
                console.error('Error predicting disease:', error);
            }
        }
    </script>
</body>
</html>
