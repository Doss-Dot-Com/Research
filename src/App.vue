<template>
  <div class="landing-page" v-if="!hasScrolled">
    <div class="details-container">
      <div v-if="showUserInfo" class="info-container">
        <p>User Information:</p>
        <div class="grid-container">
          <div v-for="(item, index) in info" :key="index" class="info-item">
            <span class="label">{{ item.label }}</span>
            <span class="value typing" :class="{
              cursorVisible: item.cursorVisible,
              cursorBlinking: item.cursorBlinking,
            }">
              {{ item.animatedValue }}
            </span>
            <br />
            <br />
          </div>
        </div>
      </div>

      <div v-for="(item, index) in info2" :key="index" v-if="showResearchByBYU" class="matrix-style">
        <span class="typing" :class="{
          cursorVisible: item.cursorVisible,
          cursorBlinking: item.cursorBlinking,
        }">
          {{ item.animatedValue }}
        </span>
      </div>

      <div v-if="generatingQRCode" class="qr-code-container wave">
        <div v-for="(row, rowIndex) in qrCodePattern" :key="rowIndex" class="qr-row">
          <div v-for="(cell, cellIndex) in row" :key="cellIndex" :class="{ 'qr-cell': true, filled: cell, wave: cell }">
          </div>
        </div>
      </div>

      <div v-if="showMatrixEffect" class="matrix-style">
        {{ animatedMatrixSentence }}
      </div>
    </div>
  </div>
<transition name="fade" mode="out-in">
  <div class="content">
    <div class="image-container">
      <img :src="currentImage" alt="Slideshow Image" class="themed-image" />
    </div>
  </div>
</transition>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import img1 from './assets/img/qrSample1.jpg';
import img2 from './assets/img/qrSample2.jpg';
import img3 from './assets/img/qrSample3.jpg';
import img4 from './assets/img/qrSample4.jpeg';
import img5 from './assets/img/qrSample5.jpg';
import img6 from './assets/img/qrSample6.png';
import img7 from './assets/img/qrSample7.jpg';
import img8 from './assets/img/qrSample8.jpg';
import img9 from './assets/img/qrSample9.jpg';
import img10 from './assets/img/qrSample10.jpeg';
import img11 from './assets/img/qrSample11.jpg';
import img12 from './assets/img/qrSample12.jpg';
import img13 from './assets/img/qrSample13.jpg';

const images = [img1, img2, img3, img4, img5, img6, img7, img8, img9, img10, img11, img12, img13];
const currentImage = ref(images[0]);
let imageIndex = 0;

function changeImage() {
  imageIndex = (imageIndex + 1) % images.length;
  currentImage.value = images[imageIndex];
}

const intervalTime = 300; // Adjusted for a more reasonable image change interval
let intervalId;

const hasScrolled = ref(false); // Corrected variable name

function handleScroll() {
  const scrollThreshold = 35;
  hasScrolled.value = window.scrollY > scrollThreshold;
  if (!intervalId) {
    intervalId = setInterval(changeImage, intervalTime);
  }
  if (hasScrolled.value) {
    document.documentElement.classList.add('content-visible');
  } else {
    document.documentElement.classList.remove('content-visible');
  }
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll);
});

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll);
  if (intervalId) {
    clearInterval(intervalId);
  }
});
</script>

<script>
import "./style.css";
export default {
  data() {
    return {
      qrCodePattern: [],
      info: [
        { label: "IP Address:", value: "10.37.196.70", animatedValue: "", cursorVisible: true, cursorBlinking: false, blur: false, },
        { label: "City:", value: "Provo", animatedValue: "", cursorVisible: false, cursorBlinking: false, blur: false, },
        { label: "Region:", value: "Utah", animatedValue: "", cursorVisible: false, cursorBlinking: false, blur: false, },
        { label: "ZIP Code:", value: "84604", animatedValue: "", cursorVisible: false, cursorBlinking: false, blur: false, },
        { label: "Country:", value: "USA", animatedValue: "", cursorVisible: false, cursorBlinking: false, blur: false, },
        { label: "School:", value: "Brigham Young University", animatedValue: "", cursorVisible: false, cursorBlinking: false, blur: false, },
        { label: "Full Name:", value: "Target Victim", animatedValue: "", cursorVisible: false, cursorBlinking: false, blur: false, },
        { label: "Phone Number:", value: "*************", animatedValue: "", cursorVisible: false, cursorBlinking: false, blur: false, },
        { label: "Email:", value: "************@*****.***", animatedValue: "", cursorVisible: false, cursorBlinking: false, blur: false, },
        { label: "Device Password:", value: "*****", animatedValue: "", cursorVisible: false, cursorBlinking: true, blur: false, },
      ],
      info2: [
        { value: 'You just gave away your information', animatedValue: "", cursorVisible: true, cursorBlinking: false, },
        { value: 'Avoid it.', animatedValue: "", cursorVisible: false, cursorBlinking: true, },
        { value: 'BYU CyberSecurity Major Research Team', animatedValue: "", cursorVisible: false, cursorBlinking: true, }

      ],

      currentInfoIndex: 0,
      showAccessGranted: false,
      showUserInfo: true,
      showingMatrixAnimation: false,
      showResearchByBYU: false,
      generatingQRCode: false,
      matrixSentence: "Could you trust this QR code?",
      animatedMatrixSentence: "",
    };
  },

  methods: {
    animateInfoValue() {
      if (this.currentInfoIndex < this.info.length) {
        const infoItem = this.info[this.currentInfoIndex];
        let i = 0;
        const speed = 20;
        infoItem.animatedValue = "█";

        const animate = () => {
          if (i < infoItem.value.length) {
            infoItem.animatedValue =
              infoItem.animatedValue.slice(0, i) + infoItem.value.charAt(i) + "█";
            i++;
            setTimeout(animate, speed);
          } else {
            infoItem.animatedValue = infoItem.animatedValue.slice(0, -1);
            if (this.currentInfoIndex === this.info.length - 1) {
              infoItem.cursorBlinking = true;
              setTimeout(() => {
                this.eraseData();
                infoItem.cursorBlinking = false;
              }, 20000);
            }

            // Typing for this item is done
            if (this.currentInfoIndex < this.info.length - 1) {
              // Non-last item
              this.currentInfoIndex++;
              this.info[this.currentInfoIndex].cursorVisible = true; // Activate cursor for the next item
              setTimeout(this.animateInfoValue, 500);
            } else {
              // Last item
              setTimeout(() => {
                this.showAccessGranted = true; // Show "Access Granted" message
              }, 500);
            }
          }
        };
        if (this.currentInfoIndex < this.info.length) {
          this.info[this.currentInfoIndex].animatedValue = "█"; // Initialize with the cursor character
          animate();
        }
      }
    },

    animateInfo2Value() {
      this.currentInfoIndex = 0; // Reset index for animating info2
      const animateNextItem = () => {
        if (this.currentInfoIndex < this.info2.length) {
          const infoItem = this.info2[this.currentInfoIndex];
          let i = 0;
          infoItem.animatedValue = "█"; // Initialize animation with cursor

          const animate = () => {
            if (i < infoItem.value.length) {
              infoItem.animatedValue = infoItem.animatedValue.slice(0, -1) + infoItem.value.charAt(i) + "█";
              i++;
              setTimeout(animate, 20); // Adjust timing as needed
            } else {
              // Once the animation for the current item is complete
              infoItem.animatedValue = infoItem.animatedValue.slice(0, -1); // Remove cursor at the end
              this.currentInfoIndex++; // Move to the next item
              if (this.currentInfoIndex < this.info2.length) {
                setTimeout(animateNextItem, 1000); // Add delay before starting next item
              }
            }
          };
          animate();
        }
      };
      animateNextItem();
    },

    generateQRCode() {
      const size = 21;
      this.qrCodePattern = Array.from({ length: size }, () =>
        Array.from({ length: size }, () => false)
      );

      const placePattern = (qrCodePattern, pattern, startX, startY) => {
        for (let row = 0; row < pattern.length; row++) {
          for (let col = 0; col < pattern.length; col++) {
            const targetRow = startX + row;
            const targetCol = startY + col;
            if (
              targetRow < qrCodePattern.length &&
              targetCol < qrCodePattern[targetRow].length
            ) {
              qrCodePattern[targetRow][targetCol] = pattern[row][col];
            } else {
              console.error(
                "Attempting to place pattern out of bounds:",
                targetRow,
                targetCol
              );
            }
          }
        }
      };

      const generatePatternWithSmallBox = (patternSize, innerBoxSize) => {
        const pattern = Array.from({ length: patternSize }, () =>
          Array.from({ length: patternSize }, () => false)
        );

        for (let i = 0; i < patternSize; i++) {
          pattern[0][i] = true;
          pattern[patternSize - 1][i] = true;
          pattern[i][0] = true;
          pattern[i][patternSize - 1] = true;
        }

        const offset = (patternSize - innerBoxSize) / 2;
        for (let i = offset; i < offset + innerBoxSize; i++) {
          for (let j = offset; j < offset + innerBoxSize; j++) {
            pattern[i][j] = true;
          }
        }

        return pattern;
      };

      const isProtectedArea = (row, col) => {
        const protectedSize = 8;
        const alignmentPatternStart = size;
        return (
          (row < protectedSize && (col < protectedSize || col >= size - protectedSize)) ||
          (row >= size - protectedSize && col < protectedSize) ||
          (row >= alignmentPatternStart && col >= alignmentPatternStart)
        );
      };

      for (let row = 0; row < size; row++) {
        for (let col = 0; col < size; col++) {
          if (!this.qrCodePattern[row][col] && !isProtectedArea(row, col)) {
            this.qrCodePattern[row][col] = Math.random() < 0.5;
          }
        }
      }

      // Generate the finder and alignment patterns
      const finderPattern = generatePatternWithSmallBox(7, 3);
      const alignmentPattern = generatePatternWithSmallBox(5, 1);

      // Place the finder patterns in the corners
      placePattern(this.qrCodePattern, finderPattern, 0, 0);
      placePattern(this.qrCodePattern, finderPattern, size - 7, 0);
      placePattern(this.qrCodePattern, finderPattern, 0, size - 7);

      // Place the alignment pattern
      placePattern(this.qrCodePattern, alignmentPattern, size - 8, size - 8);
    },

    generatePixels(imageSource, numPixelsWide, numPixelsTall, pixelSize) {
      const container = this.$el.querySelector('#image-container');
      const totalPixels = numPixelsWide * numPixelsTall;

      for (let i = 0; i < totalPixels; i++) {
        const pixel = document.createElement('div');
        pixel.classList.add('pixel');
        pixel.style.width = `${pixelSize}px`;
        pixel.style.height = `${pixelSize}px`;
        pixel.style.backgroundImage = `url('${imageSource}')`;
        const x = (i % numPixelsWide) * pixelSize;
        const y = Math.floor(i / numPixelsWide) * pixelSize;
        pixel.style.backgroundPosition = `-${x}px -${y}px`;
        pixel.style.opacity = 0;
        container.appendChild(pixel);
      }
      this.revealPixels(totalPixels);
    },

    revealPixels(totalPixels) {
      const pixels = this.$el.querySelectorAll('.pixel');
      let count = 0;
      const interval = setInterval(() => {
        if (count >= totalPixels) {
          clearInterval(interval);
          return;
        }
        pixels[count].style.opacity = 1;
        count++;
      }, 10); // Adjust time as needed
    },

    startQRCodeGeneration() {
      this.generatingQRCode = true;
      this.generateQRCode();
    },

    eraseData() {
      let index = 0;
      const eraseSpeed = 100;
      const erase = () => {
        if (index < this.info.length) {
          this.info[index].animatedValue = "";
          index++;
          setTimeout(erase, eraseSpeed);
        }
      };
      erase();
    },

    startMatrixAnimation() {
      const baseSentence = " you trust this QR code?"; // Added space before 'you'
      const words = ["Could", "Would", "Should"];
      let currentWordIndex = 0;
      let animatedSentence = ''.padStart(words[0].length, ' ');
      let index = 0;

      const scramble = () => {
        let finalWord;
        if (currentWordIndex === -1) {
          finalWord = words[0] + baseSentence;
        } else {
          finalWord = words[currentWordIndex] + baseSentence; // Only the first word for subsequent animations
        }

        if (index < finalWord.length) {
          let randomChar; // Random char from ASCII range
          if (Math.random() < 0.9) {
            randomChar = finalWord.charAt(index);
          } else {
            randomChar = String.fromCharCode(65 + Math.random() * 26);
          }

          animatedSentence = animatedSentence.substr(0, index) + randomChar + animatedSentence.substr(index + 1);
          this.animatedMatrixSentence = animatedSentence;

          if (randomChar === finalWord.charAt(index)) {
            index++;
          }

          setTimeout(scramble, 50); // Adjust the speed of animation here
        } else {
          index = 0;
          currentWordIndex = (currentWordIndex + 1) % words.length;
          animatedSentence = ''.padStart(words[currentWordIndex].length, ' ');
          setTimeout(scramble, 2000);
        }
      };
      scramble();
    }
  },

  mounted() {
    this.animateInfoValue(); // User information

    setTimeout(() => {
      this.showUserInfo = false;
      this.showResearchByBYU = true;
      this.animateInfo2Value();
    }, 10000);

    setTimeout(() => {
      this.showResearchByBYU = false;
      this.showMatrixEffect = true; // Show Matrix Animation Container
      this.showUserInfo = false; // Hide User Information

      this.startQRCodeGeneration(); // Generate QR Code 
      setTimeout(() => {
        this.startMatrixAnimation();
      }, 500) // QR Code Sentence Generation 
    }, 20000);
  },
};
</script>

<style>
@keyframes blink {
  50% {
    visibility: hidden;
  }
}

@keyframes wave {
  0% {
    background-position: 0% 50%;
  }

  100% {
    background-position: 100% 50%;
  }
}

@keyframes waveEffect {
  0% {
    background-position: 0% 50%;
  }

  100% {
    background-position: 100% 50%;
  }
}

.blur {
  filter: blur(5px);
}

.blur-effect {
  filter: blur(5px);
}

.content {
  transition: transform 2s ease, opacity 2s ease;
  opacity: 0;
  transform: translateY(50vh);
  pointer-events: none;
}

.content-visible .landing-page {
  opacity: 0;
  transform: translateY(50vh);
}

.content-visible .content {
  opacity: 1;
  transform: translateY(0);
  pointer-events: all;
}

.cyan-filtered-image {
  width: 80%;
  filter: sepia(1) saturate(1000%) hue-rotate(160deg);
  opacity: 0.5;
}

.details-container {
  font-family: "DinaRemaster", monospace;
  font-size: 1rem;
  height: 431px;
  display: flex;
  flex-direction: column;
  background: radial-gradient(ellepsis, rgb(0, 36, 36) 10%, rgba(0, 0, 0, 0.8) 0%);
  background: repeating-linear-gradient(to right, #000 0px, #001d1d 1px, #000 2px);
  animation: wave 2ms linear infinite;
  background-size: 200% 100%;
  text-align: left;
  padding: 20px;
  margin: 20px;
  margin-left: auto;
  margin-right: auto;
  text-shadow: 0 0 1px #00d1d1, 0 0 4px #00d1d1;
  box-shadow: inset 0 0 5px rgba(0, 107, 107, 0.1), inset 0 0 15px rgba(0, 97, 97, 0.1),
    inset 0 0 10px rgba(0, 36, 36, 0.9), inset 0 0 70px rgba(0, 36, 36, 0.9);
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 2s ease;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
.glowing-line {
  position: absolute;
  left: 50%;
  /* Start at the center of the container */
  width: calc(50% + 100px);
  /* Adjust based on your content */
  height: 2px;
  /* Thickness of the line */
  background: rgba(0, 209, 209, 0.9);
  /* Line color */
  box-shadow: 0 0 10px rgba(0, 209, 209, 0.9);
  /* Glow effect */
  transform: translateX(-50%);
  /* Center align */
}

.glowing-text {
  color: #00d1d1;
  text-shadow: 0 0 1px #00e6e6, 0 0 4px #00e6e6;
}

.grid-container {
  display: grid;
  grid-template-columns: auto 1fr;
  /* Adjust the column sizes as needed */
  grid-column-gap: 20px 1fr;
  line-height: 3.5px;
  grid-row-gap: 5px;
  /* Adjust the row sizes as needed */
  width: 360px;
}

.info-item {
  display: contents;
}

.info-container {
  display: center;
}

.image-container img {
  width: 250px;
  height: 250px;
}

.landing-page {
  transition: opacity 2s ease, transform 2s ease;
  opacity: 1;
  transform: translateY(0);
}

.label {
  font-family: "DinaRemaster", monospace;
  grid-column: 1;
  /* Place the label in the first column */
  display: flex;
  align-items: center;
  padding-right: 1em;
  /* Space between label and value */
}

.matrix-style {
  color: #00d1d1;
  /* Green text */
  font-family: "DinaRemaster", monospace;
  background-color: 000;
  padding: 20px;
  line-height: 1.1;
  text-align: center;
  font-size: 18px;
  /* Additional styling as needed */
}

.qr-code-container {
  display: grid;
  grid-template-columns: repeat(21, 10px);
  border-radius: 100px;
  grid-gap: 0px;
  justify-content: center;
  margin-top: 20px;
  background-color: #00e5e500;
}

.qr-row {
  display: contents;
}

.qr-cell {
  width: 10px;
  height: 10px;
}

.qr-cell.filled {
  background-color: #00d1d1;
}

.qr-cell.wave {
  animation: waveEffect 10s forwards;
}

.themed-image {
  filter: contrast(1.1) saturate(0.9) brightness(1.2);
  mix-blend-mode: luminosity;
}

.typing {
  font-family: "DinaRemaster", monospace;
  white-space: pre;
  position: relative;
  overflow: hidden;
  text-shadow: 0 0 1px #002424, 0 0 4px #002424;
}

.typing::after {
  content: "";
  position: absolute;
  right: 0;
  color: rgba(0, 209, 209, 0.9);
  /* Color of your input field */
  opacity: 1;
  /* Fully visible when shown */
  text-shadow: none;
}

.typing.cursor::after {
  content: "█";
  position: absolute;
  right: 0;
  color: #001818;
  visibility: visible;
  /* Initially hide the cursor */
}

.typing.cursorBlinking::after {
  content: "█";
  position: relative;
  right: 0;
  color: #002424;
  /* Color of your input field */
  opacity: 1;
  /* Fully visible when shown */
  animation: blink 1s step-end infinite;
  visibility: hidden;
  /* Apply the blinking animation */
}

.typing.cursorVisible::after {
  visibility: visible;
  /* Show the cursor when typing */
}

.value {
  display: flex;
  align-items: center;
  font-family: "DinaRemaster", monospace;
  background-color: rgba(0, 209, 209, 0.8);
  text-shadow: 0 0 1px #002424, 0 0 1px #002424;
  box-shadow: 0 0 10px rgba(0, 209, 209, 0.4);
  /* Glow effect */
  border-radius: 2px;
  color: #002424;
  padding: 0.1rem 0.5rem;
  max-width: 210px;
  height: 20px;
  overflow: hidden;
  grid-column: 2;
  /* Place the value in the second column */
  text-align: left;
  /* Align the value text to the left */
  white-space: nowrap;
  /* Prevent the value from wrappig */
  text-overflow: ellipsis;
  position: relative;
  /* For the cursor positioning */
}
</style>
