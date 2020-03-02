<template>
  <div class="mario-ladder">
    <div class="audio-container">
      <img src="@/assets/sound_on.png" alt="">
      <img src="@/assets/sound_on.png" alt="">
    </div>
    <div class="title-container">
      <img src="@/assets/title.png" alt="">
    </div>
    <div class="main-container">
      <div class="main__game-container">
        
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
      <div class="main__bet-container">
        <div class="bet__buttons-container">
          <button class="bet__button">Left</button>
          <button class="bet__button">Right</button>
          <button class="bet__button">4</button>
          <button class="bet__button">3</button>
          <button class="bet__button-start">START</button>
        </div>
        <div class="bet__progress-container">
          <div class="bet-title">Lorem Ipsum</div>
          <div class="progress-container">
            <div class="progress-title">1 v 2</div>
            <div class="progress-bar progress-1"></div>
            <div class="progress-bar progress-2"></div>
          </div>
          <div class="progress-container">
            <div class="progress-title">3 v 4</div>
            <div class="progress-bar progress-1"></div>
            <div class="progress-bar progress-2"></div>
          </div>
          <div class="progress-container">
            <div class="progress-title">5 v 6</div>
            <div class="progress-bar progress-1"></div>
            <div class="progress-bar progress-2"></div>
          </div>
        </div>
      </div>
      <div class="main__history-container">
        <div class="history-container">
          <div class="history-title">Lorem Ipsum</div>
          <div class="history-content">Lorem ipsum dolor sit amet, consectetur</div>
        </div>
      </div>
    </div>
    <div class="footer-container">
      <button>Lorem Ipsum</button>
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

@mixin tablet {
  @media screen and (min-width: 420px) {
    @content;
  }
}

@mixin desktop {
  @media screen and (min-width: 1200px) {
    @content;
  }
}

// Markup Styles
.mario-ladder {
  background: rgb(242, 255, 194);
  display: block;
  width: 90%;
  padding: 0;
  margin: 0;
  margin: 0 auto;
  box-sizing: border-box;

  button {
    border-radius: 10px;
    padding: 5px 10px;
    outline: none;
    border: none;
  }

  .audio-container {
    height: 100%;
    background: rgb(255, 159, 159);
    display: block;
    padding: 5px;
    text-align: right;

    img:first-child {
      margin-right: 5px;
    }
  }

  .title-container {
    display: block;
    background: rgb(189, 189, 255);
    padding: 10px; 

    img {
      max-width: 100%;
      max-height: 70px;
    }
  }

  .main-container {
    & > * {
      width: 100%;
      border: 1px solid #000;
      padding: 20px;
    }

    .main__game-container {
      svg {
        max-height: 450px;
        max-width: 100%;

        @include tablet {
          max-height: 300px;
        };

        @include tablet {
          max-height: 700px;
        };
      }
    }

    .main__bet-container {
      & > * {
        width: 100%;
        height: 100%;
        padding: 10px;
      }

      .bet__buttons-container {
        background: rgba(0,0,0,0.8);
        margin-bottom: 5px;

        .bet__button {
          width: 100%;
          text-transform: uppercase;

          &:not(:last-child) {
            margin-bottom: 5px;
          }
        }
      }

      .bet__progress-container {
        background: rgba(0,0,0,0.8);

        .bet-title {
          color: white;
          font-size: 1.2rem;
          font-weight: bold;
        }

        .progress-container {
          background: #fff;
          margin-top: 10px;
          width: 100%;
          padding: 2px 5px;

          &:not(:last-child) {
            margin-bottom: 10px;
          }

          .progress-bar {
            height: 20px;
            width: 100%;
            display: inline-block;
            background: rgb(255, 156, 234);
          }
        }
      }
    }

    .main__history-container {
      padding: 20px;

      .history-container {
        color: white;
        background: rgba(0,0,0,0.8);
        padding: 10px;
        height: 100%;
        overflow-y: auto;

        .history-title {
          font-size: 1.2rem;
          font-weight: bold;
        }

        .history-content {
          margin-top: 10px;
        }
      }
    }
  }

  .footer-container {
    display: block;
    background: rgb(160, 155, 155);
    padding: 10px;
    text-align: right;
  }
}


// Grid Support
@supports(display: grid) {
  @include tablet {
    .mario-ladder {
      display: grid;
			grid-template-columns: 1fr 1fr 1fr;
			grid-template-rows: auto auto 5fr auto;
      grid-template-areas:
        "audio audio audio"
        "title title title"
        "main main main"
        "footer footer footer";
    }

    .audio-container {
      grid-area: audio;
    }

    .title-container {
      grid-area: title;
    }

    .main-container {
      grid-area: main;
      display: grid;
			grid-template-columns: 1fr 1fr;
			grid-template-rows: auto auto ;
      grid-template-areas: 
        "game game"
        "bet history";

      .main__game-container {
        grid-area: game;
      }

      .main__bet-container {
        grid-area: bet;

        display: grid;
        grid-template-columns: 1fr;
        grid-template-rows: auto auto;
        grid-row-gap: 20px;
        grid-template-areas: 
          "buttonsArea"
          "progressArea";

        .bet__buttons-container {
          grid-area: buttonsArea;

          display: grid;
          grid-template-columns: 1fr 1fr;
          grid-template-rows: 1fr 1fr 1fr;
          grid-gap: 3px;
          grid-template-areas: 
            "controlButton controlButton"
            "controlButton controlButton"
            "start start";

          .bet__button-start {
            grid-area: start;
          }
        }

        .bet__progress-container {
          grid-area: progressArea;

          .progress-container {
            display: grid;
            grid-template-columns: 1fr 1fr;
            grid-gap: 10px;
            grid-template-areas: 
              "title title"
              "progressBar1 progressBar2";

              .progress-title {
                grid-area: title;
              }

              .progress-bar {
                &.progress-1 {
                  grid-area: progressBar1;
                }
                &.progress-2 {
                  grid-area: progressBar2;
                }
              }
          }
        }
      }

      .main__history-container {
        grid-area: history;
      }
    }

    .footer-container {
      grid-area: footer;
    }
  }

  @media screen and (min-width: 1200px) {
    .mario-ladder {
			.main-container {
        grid-template-columns: 1fr 1fr 1fr;
        grid-template-areas: "bet game history";
      }
    }
  }
}
</style>