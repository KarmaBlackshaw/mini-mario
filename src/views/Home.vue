<template>
  <div class="mario-ladder">
    <div class="betting-container">
      <div class="betting-buttons-container">
        <button
          @click="controller.direction = 'left'"
          :class="{ active: controller.direction === 'left' }"
          v-text="'Left'"
        />
        <button
          @click="controller.direction = 'right'"
          :class="{ active: controller.direction === 'right' }"
          v-text="'Right'"
        />
        <button
          @click="controller.lines = 3"
          :class="{ active: controller.lines === 3 }"
          v-text="'Three'"
        />
        <button
          @click="controller.lines = 4"
          :class="{ active: controller.lines === 4 }"
          v-text="'Four'"
        />
        <button class="btn--start" @click="startAnimation">Start</button>
      </div>
      <div class="betting-box">
        <div class="betting-box__title">Lorem ipsum</div>

        <div class="betting-box__item">
          <div class="bb-item__title">1 vs 2</div>
          <div class="bb-item__progress-container">
            <div class="progress-1">
              <div class="bar"></div>
            </div>
            <div class="progress-2">
              <div class="bar"></div>
            </div>
          </div>
        </div>

        <div class="betting-box__item">
          <div class="bb-item__title">1 vs 2</div>
          <div class="bb-item__progress-container">
            <div class="progress-1">
              <div class="bar"></div>
            </div>
            <div class="progress-2">
              <div class="bar"></div>
            </div>
          </div>
        </div>

        <div class="betting-box__item">
          <div class="bb-item__title">1 vs 2</div>
          <div class="bb-item__progress-container">
            <div class="progress-1">
              <div class="bar"></div>
            </div>
            <div class="progress-2">
              <div class="bar"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="game-container">
      <div class="game-content">
        <img src="@/assets/title.png" class="game-title" alt="" />
        <div class="loading-container" v-if="!isPlaying">
          <img src="@/assets/loading.gif" class="loading-img" alt="" />
          <div class="loading-text">{{ time }}</div>
        </div>
        <svg viewBox="0 0 249.8 423.35" class="svg" id="svg" :key="svgKey">
          <defs>
            <image
              id="image"
              width="18"
              height="17"
              href="@/assets/ball_3.png"
            />
          </defs>
          <path :d="getLinePoints.path" style="fill:#fff" />
          <polyline
            :points="getLinePoints.solution"
            id="solution"
            style="fill:none;stroke:#000;stroke-miterlimit:10;stroke-width:7px;visibility:hidden"
          />

          <image
            width="114"
            height="156"
            transform="translate(150.25 287.13) scale(0.87)"
            href="@/assets/bar_mario.png"
          />
          <image
            width="89"
            height="151"
            transform="translate(-2, 292.89) scale(0.86)"
            href="@/assets/bar_plant.png"
          />
          <use transform="translate(27.86 0)" xlink:href="#image" />
          <use transform="translate(213.53)" xlink:href="#image" />
        </svg>
      </div>
    </div>

    <div class="history-container">
      <div class="history-box">
        <div class="history-box__title">Lorem ipsum</div>
        <div class="history-box__content">
          <div class="grid" v-for="(val, key) in history" :key="key">
            <span>
              {{ key + 1 }}
            </span>
            <img
              :src="require(`@/assets/history_${val.direction}.png`)"
              width="30"
              alt=""
            />
            <img
              :src="require(`@/assets/history_${val.lines}.png`)"
              width="30"
              alt=""
            />
            <img
              :src="require(`@/assets/history_${val.result}.png`)"
              width="30"
              alt=""
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as Vivus from "vivus"

export default {
  data: () => ({
    fourLines: {
      path:
        "M219.36,15V78h-179V15h-7V381h7V277h179V381h7V15Zm0,255h-179V215h179Zm0-62h-179V152h179Zm0-63h-179V85h179Z",
      left:
        "36.86 14.52 36.86 81.05 129.69 81.05 222.53 81.05 222.53 147.55 36.86 147.55 36.86 211.05 222.53 211.05 222.53 273.29 36.86 273.29 36.86 380.69",
      right:
        "222.53 15.19 222.53 81.73 129.69 81.73 36.86 81.73 36.86 148.23 222.53 148.23 222.53 211.73 36.86 211.73 36.86 273.97 222.53 273.97 222.53 381.36"
    },
    threeLines: {
      path:
        "M219,15.54v63H40.36v-63h-7V381.7h7V277.81H219V381.7h7V15.54Zm0,255.27H40.36V180.23H219Zm0-97.57H40.36V85.57H219Z",
      left:
        "36.86 15.54 36.86 82.07 222.53 82.07 222.53 176.74 36.86 176.74 36.86 274.31 222.53 274.31 222.53 381.7",
      right:
        "222.53 15.54 222.53 82.07 36.86 82.07 36.86 176.74 222.53 176.74 222.53 274.31 36.86 274.31 36.86 381.7"
    },

    controller: {
      lines: 4,
      direction: "right",
      result: ""
    },

    svgKey: 1,

    isPlaying: false,
    vivus: null,

    time: 5,
    history: []
  }),

  computed: {
    getLinePoints() {
      const { lines, direction } = this.controller

      if (lines === 4) {
        return {
          path: this.fourLines.path,
          solution: this.fourLines[direction]
        }
      }

      if (lines === 3) {
        return {
          path: this.threeLines.path,
          solution: this.threeLines[direction]
        }
      }

      return {}
    }
  },

  created() {
    this.setData()
    // setInterval(() => {
    //   this.time -= 1
    //   if (this.time === 0) {
    //     this.startAnimation()
    //   }
    // }, 1000)
  },

  methods: {
    getApiData() {
      const data = [
        { r: 271, result: ["RIGHT", "LINE4", "EVEN"] },
        { r: 275, result: ["LEFT", "LINE3", "EVEN"] },
        { r: 280, result: ["LEFT", "LINE4", "ODD"] }
      ]

      return data[Math.floor(Math.random() * 3)]
    },

    setData() {
      const { result } = this.getApiData()

      if (!["RIGHT", "LEFT"].includes(result[0])) {
        console.error("Invalid Direction")
      }

      if (!["LINE3", "LINE4"].includes(result[1])) {
        console.error("Invalid Lines")
      }

      if (!["EVEN", "ODD"].includes(result[2])) {
        console.error("Invalid Lines")
      }

      this.controller.direction = result[0] === "RIGHT" ? "right" : "left"
      this.controller.lines = result[1] === "LINE3" ? 3 : 4
      this.controller.result = result[2].toLowerCase()
    },

    startAnimation() {
      document.getElementById("solution").style.visibility = "visible"

      this.isPlaying = true

      new Vivus(
        "svg",
        {
          duration: 300,
          type: "sync"
        },
        () => {
          this.history.push({
            lines: this.controller.lines,
            direction: this.controller.direction,
            result: this.controller.result
          })

          this.setData()
          this.isPlaying = false
          this.svgKey++
          this.time = 5
        }
      )
    }
  }
}
</script>

<style lang="scss" scoped>
@keyframes load {
  to {
    width: 100%;
  }
}

@keyframes animateStripes {
  to {
    background-position: 190px 0;
    width: 100%;
  }
}

@mixin mobile {
  @media screen and (max-width: 480px) {
    @content;
  }
}

@mixin tablet {
  @media screen and (max-width: 768px) {
    @content;
  }
}

@mixin desktop {
  @media screen and (min-width: 769px) {
    @content;
  }
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

$game-height: 700px;

.mario-ladder {
  margin: auto;
  // display: flex;
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  justify-items: center;

  height: 100%;
  width: 80%;
  background-image: url("../assets/background.png");
  background-position: center center;
  background-repeat: no-repeat;
  background-size: 100% 100%;

  @include tablet {
    width: 90%;
    background-size: cover;
  }

  @include mobile {
    width: 100%;
    height: 100vh;
    background: #ffeaa7;
  }

  & > * {
    // border: 1px solid #000;
    height: 100%;
    width: 100%;
  }

  .betting-container {
    // flex: 1;
    position: relative;

    @include mobile {
      // display: none;
      grid-column: 1/4;
      grid-row: 2/3;
      display: grid;
      grid-template-columns: 1fr 1fr;
    }

    & > * {
      width: 90%;
      background-color: rgba(0, 0, 0, 0.8);
      border: 1px solid #000;
      padding: 20px 10px;
      margin: 0 auto;

      @include mobile {
        width: 80%;
        height: auto;
        padding: 10px 5px;
      }
    }

    .betting-buttons-container {
      display: grid;
      grid-template-columns: 1fr 1fr;
      grid-column-gap: 5px;
      margin-top: 60px;

      @include mobile {
        margin-top: 0;
        height: 100%;
        // display: none;
      }

      button {
        margin: 2px;
        border: none;
        text-transform: uppercase;
        width: 100%;
        font-size: 0.9rem;
        height: 30px;
        border-radius: 5px;
        background-color: #2ecc71;
        color: darken($color: #27ae60, $amount: 30);
        font-weight: bold;
        letter-spacing: 1px;
        transition: all 0.2s ease-in-out;

        &:hover {
          background-color: darken($color: #2ecc71, $amount: 2);
          box-shadow: inset 0.5px 0.5px 5px 1px rgba(0, 0, 0, 0.5);
        }

        &:active,
        &:focus {
          outline: none;
        }

        &.btn--start {
          width: 100%;
          background-color: #f1c40f;
          grid-column: 1/3;
        }

        &.active {
          background-color: rgb(183, 0, 255);
          color: white;
          box-shadow: inset 0.5px 0.5px 5px 1px rgba(0, 0, 0, 0.5);
        }
      }
    }

    .betting-box {
      color: rgb(248, 255, 151);
      text-align: center;

      @include desktop {
        position: absolute;
        bottom: 60px;
        left: 50%;
        transform: translateX(-50%);
      }

      .betting-box__title {
        font-size: 1.5rem;
        margin-bottom: 15px;
      }

      .betting-box__item {
        margin: 0 auto;
        margin-bottom: 10px;

        .bb-item__title {
          color: #fff;
          font-size: 1rem;
          display: block;
          margin-bottom: 5px;
          font-weight: bold;
        }

        .bb-item__progress-container {
          display: flex;
          justify-content: center;

          .progress-1,
          .progress-2 {
            width: 50%;
            height: 20px;
            background-color: #fff;
            overflow: hidden;

            .bar {
              height: 100%;
              width: 0%;
              animation: 2s animateStripes linear infinite,
                3s load linear infinite;
              background-size: 29px 29px;
              background-attachment: fixed;
            }
          }

          .progress-1 {
            margin-right: 3px;
            border-top-left-radius: 50px;
            border-bottom-left-radius: 50px;
            display: flex;
            justify-content: flex-end;

            .bar {
              background-image: repeating-linear-gradient(
                -45deg,
                #c0392b,
                #c0392b 10px,
                #e74c3c 10px,
                #e74c3c 20px
              );
            }
          }

          .progress-2 {
            margin-left: 3px;
            border-top-right-radius: 50px;
            border-bottom-right-radius: 50px;

            .bar {
              background-image: repeating-linear-gradient(
                45deg,
                #2980b9,
                #2980b9 10px,
                #3498db 10px,
                #3498db 20px
              );
            }
          }
        }
      }
    }
  }

  .game-container {
    @include mobile {
      width: 100vw;
    }

    .loading-container {
      position: absolute;
      top: 55%;
      left: 50%;
      transform: translate(-50%, -50%);

      @include mobile {
        left: 51%;
      }

      @include mobile {
        width: 70%;
      }

      .loading-img {
        width: 100%;
      }

      .loading-text {
        position: absolute;
        bottom: 0px;
        padding: 0 1%;
        height: 75px;
        text-align: center;
        font-size: 1.2rem;
        font-weight: bold;
        margin: 0 10%;
        width: 80%;

        @include mobile {
          height: 40px;
        }
      }
    }

    .game-content {
      position: relative;

      .game-title {
        margin: 20px auto;
        display: block;
        user-select: none;
        pointer-events: none;
      }

      .svg {
        height: 100%;
        width: 280px;
        padding-right: 5px;

        @include mobile {
          width: 300px;
        }
      }
    }
  }

  .history-container {
    // flex: 1;
    position: relative;

    @include mobile {
      display: none;
    }

    .history-box {
      color: rgb(248, 255, 151);
      text-align: center;
      width: 90%;
      height: $game-height - 120px;
      background: rgba(0, 0, 0, 0.8);
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);

      .history-box__title {
        font-size: 1.5rem;
        margin-bottom: 10px;
        margin-top: 10px;
      }

      .history-box__content {
        overflow-y: auto;
        background: rgb(248, 255, 151);
        padding: 15px;
        margin-left: 15px;
        margin-right: 15px;
        height: 90%;
        border-radius: 10px;

        &::-webkit-scrollbar {
          width: 12px;
        }

        &::-webkit-scrollbar-track {
          box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
          border-radius: 10px;
        }

        &::-webkit-scrollbar-thumb {
          border-radius: 10px;
          box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
        }

        .grid {
          margin-bottom: 1em;
          display: grid;
          grid-template-columns: 9fr 1fr 1fr 1fr;
          grid-gap: 1em;

          span {
            color: black;
            font-size: 1rem;
            font-weight: 600;
          }

          img {
            pointer-events: none;
          }
        }
      }
    }
  }
}
</style>
