<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wheel of Fortune</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            font-family: 'Harrington', sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-image: url('c:/Users/zores/Downloads/noelle-BK4rszchcE4-unsplash.jpg'); /* Update the path */
            background-size: cover;
            background-position: center;
        }

        #particles-js {
            width: 100%;
            height: 100%;
            position: absolute;
            z-index: -1;
        }

        #wheel-container,
        #result-container,
        #countdown-container {
            text-align: center;
            padding: 20px;
            border-radius: 10px;
            color: #d01313; /* Change the font color for the text */
            background-color: rgba(255, 255, 255, 0.5); /* Semi-transparent white background */
        }

        h1 {
            font-size: 38px; /* Change the font size for the header */
        }

        button {
            font-size: 26px; /* Change the font size for the button */
	    font-family: 'Harrington', sans-serif;
            color: #fff; /* Change the font color for the button */
            background-color: #d01313; /* Change the background color for the button */
            border: none;
            padding: 10px 20px;
            margin-top: 10px;
            cursor: pointer;
            border-radius: 5px;
        }

        #result-container,
        #countdown-container {
            display: none;
            font-size: 38px;
	    font-family: 'Harrington', sans-serif;
            margin-top: 20px;
            color: #d01313; /* Change the font color for the displayed name or countdown */
        }
    </style>
    <script src="https://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js"></script>
</head>

<body>
    <audio id="background-music" loop>
        <source src="song.mp3" type="audio/mpeg"> <!-- Update the path and file type -->
        Your browser does not support the audio element.
    </audio>

    <div id="particles-js"></div>
    <div id="wheel-container">
        <h1>Witaj!<br>Niech los zadecyduje<br>dla kogo zostaniesz Mikołajem w tym roku</h1>
        <button onclick="startCountdown()">ZACZYNAMY</button>
    </div>

    <div id="countdown-container">
        <!-- Countdown will be displayed here -->
    </div>

    <div id="result-container">
        <!-- Result will be displayed here -->
    </div>

    <!-- Firebase scripts -->
    <script type="module">
        // Import the functions you need from the SDKs you need
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.0/firebase-app.js";
        import { getAnalytics } from "https://www.gstatic.com/firebasejs/10.7.0/firebase-analytics.js";
        // TODO: Add SDKs for Firebase products that you want to use
        // https://firebase.google.com/docs/web/setup#available-libraries

        // Your web app's Firebase configuration
        // For Firebase JS SDK v7.20.0 and later, measurementId is optional
        const firebaseConfig = {
            apiKey: "AIzaSyAthH5BaOJ5t06QIax0AT6mQyF9jWmLiXw",
            authDomain: "gifrts-for-christmas.firebaseapp.com",
            projectId: "gifrts-for-christmas",
            storageBucket: "gifrts-for-christmas.appspot.com",
            messagingSenderId: "619209949756",
            appId: "1:619209949756:web:ffc2d56a520cb7ca76b939",
            measurementId: "G-JHXBGKB20C"
        };

        // Initialize Firebase
        const app = initializeApp(firebaseConfig);
        const analytics = getAnalytics(app);
	const db = firebase.firestore();
    </script>



    <script>
        document.addEventListener('click', function () {
            const audio = document.getElementById('background-music');
            audio.muted = false;
            if (audio.paused) {
                audio.play();
            }
        });

        particlesJS('particles-js', {
            particles: {
                number: { value: 80, density: { enable: true, value_area: 800 } },
                color: { value: '#ffffff' },
                shape: { type: 'image', image: { src: 'snow.png', width: 50, height: 50 } }, // Change 'snowflake.png' to the actual file name
                opacity: { value: 0.5, random: false, anim: { enable: false, speed: 1, opacity_min: 0.1, sync: false } },
                size: { value: 15, random: true, anim: { enable: false, speed: 40, size_min: 0.1, sync: false } },
                line_linked: { enable: false },
                move: { enable: true, speed: 1, direction: 'bottom', random: false, straight: false, out_mode: 'out', bounce: false, attract: { enable: false, rotateX: 600, rotateY: 1200 } }
            },
            interactivity: { detect_on: 'canvas', events: { onhover: { enable: true, mode: 'repulse' }, onclick: { enable: true, mode: 'push' }, resize: true }, modes: { grab: { distance: 400, line_linked: { opacity: 1 } }, bubble: { distance: 400, size: 40, duration: 2, opacity: 8, speed: 3 }, repulse: { distance: 200, duration: 0.4 }, push: { particles_nb: 4 }, remove: { particles_nb: 2 } } },
            retina_detect: true
        });

        function startCountdown() {
            const wheelContainer = document.getElementById("wheel-container");
            const countdownContainer = document.getElementById("countdown-container");
            wheelContainer.style.display = "none";
            countdownContainer.style.display = "block";

            let count = 8; // Set the initial countdown value

            const countdownInterval = setInterval(() => {
                // Display the countdown
                countdownContainer.innerText = count;

                if (count === 0) {
                    // When countdown reaches 0, hide the countdown container and show the result
                    clearInterval(countdownInterval);
                    countdownContainer.style.display = "none";
                    showResult();
                }

                count--;
            }, 1000); // Update the countdown every second
        }

        function showResult() {
    const participantsRef = db.collection("participants");

    // Fetch data from Firestore
    participantsRef.get().then((querySnapshot) => {
        const participants = [];
        const presents = {};

        querySnapshot.forEach((doc) => {
            participants.push(doc.id);
            presents[doc.id] = doc.data().presents.split(',').map(present => present.trim());
        });

        // Display the result with data from Firestore
        const randomIndex = Math.floor(Math.random() * participants.length);
        const selectedName = participants[randomIndex];
        const selectedPresents = presents[selectedName];

        const resultContainer = document.getElementById("result-container");
        resultContainer.innerHTML = `Congratulations!<br>You will be Santa Claus for<br><span style="font-size: 52px; font-weight: bold;">${selectedName}</span>.<br>${selectedName}'s wishes under the Christmas tree: <br>${selectedPresents.join("<br>")}.`;
        resultContainer.style.display = "block";
    });
}

            const randomIndex = Math.floor(Math.random() * participants.length);
            const selectedName = participants[randomIndex];

            // Retrieve presents for the selected person
            const selectedPresents = presents[selectedName];

            // Display the result with line breaks for both name and presents
            const resultContainer = document.getElementById("result-container");
            resultContainer.innerHTML = `Gratulacje!<br>Będziesz Mikołajem dla<br><span style="font-size: 52px; font-weight: bold;">${selectedName}</span>.<br>Życzenia ${selectedName} pod choinkę: <br>${selectedPresents.join("<br>")}.`;
            resultContainer.style.display = "block";
        }
    </script>
</body>

</html>
