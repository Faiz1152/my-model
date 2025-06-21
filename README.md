<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>My Teachable Machine Image Model</title>
    <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@0.8/dist/teachablemachine-image.min.js"></script>
    <style>
      body {
        font-family: sans-serif;
        text-align: center;
        padding: 30px;
        background: #f0f0f0;
      }
      video {
        width: 300px;
        border-radius: 12px;
      }
      .result {
        font-size: 20px;
        margin-top: 20px;
        font-weight: bold;
      }
    </style>
  </head>
  <body>
    <h1>My AI Image Model</h1>
    <video id="webcam" autoplay playsinline muted></video>
    <div class="result" id="label-container"></div>

    <script type="text/javascript">
      const URL = "./model/";
      let model, webcam, labelContainer, maxPredictions;

      async function init() {
        const modelURL = URL + "model.json";
        const metadataURL = URL + "metadata.json";

        model = await tmImage.load(modelURL, metadataURL);
        maxPredictions = model.getTotalClasses();

        const flip = true;
        webcam = new tmImage.Webcam(300, 300, flip);
        await webcam.setup();
        await webcam.play();
        window.requestAnimationFrame(loop);

        document.getElementById("webcam").appendChild(webcam.canvas);
        labelContainer = document.getElementById("label-container");
      }

      async function loop() {
        webcam.update();
        await predict();
        window.requestAnimationFrame(loop);
      }

      async function predict() {
        const prediction = await model.predict(webcam.canvas);
        labelContainer.innerHTML = "";
        for (let i = 0; i < prediction.length; i++) {
          const classPrediction = prediction[i].className + ": " + (prediction[i].probability * 100).toFixed(2) + "%";
          labelContainer.innerHTML += classPrediction + "<br>";
        }
      }

      init();
    </script>
  </body>
</html>
	
