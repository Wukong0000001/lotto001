<template>
  <div id="app">
    <v-layout row wrap justify-center align-center my-3>
      <v-flex class="text-center">
        <h1 class="front mt-8" style="color: #ff6600">
          รางวัลล็อตเตอรี่ Diversition
        </h1>
      </v-flex>
    </v-layout>
    <v-layout row wrap justify-center align-center my-3>
      <v-flex class="text-center">
        <div>
          <v-flex>
            <div class="pa-10">
              <span
                class="black--text font-weight-bold"
                style="font-size: 18px"
              >
                ผลการออกรางวัลล็อตเตอรี่ Diversition
              </span>
              <div class="pa-5">
                <v-btn
                  dense
                  style="
                    border-radius: 36px;
                    border: 2px solid #ff6600;
                    background-color: transparent;
                    color: black;
                  "
                  @click="generatePrizes"
                >
                  ดำเนินการสุ่มรางวัล
                </v-btn>
              </div>
              <div class="pa-5">
                <table v-if="prizesGenerated" class="prizes-table">
                  <thead>
                    <tr>
                      <th>ประเภท</th>
                      <th>รางวัล</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr>
                      <td>รางวัลที่ 1</td>
                      <td>{{ prizes.first }}</td>
                    </tr>
                    <tr>
                      <td>รางวัลเลขข้างเคียงรางวัลที่ 1</td>
                      <td v-for="(prize, index) in prizes.nearby" :key="index">
                        {{ prize }}
                      </td>
                    </tr>
                    <tr>
                      <td>รางวัลที่ 2</td>
                      <td v-for="(prize, index) in prizes.second" :key="index">
                        {{ prize }}
                      </td>
                    </tr>
                    <tr>
                      <td>รางวัลเลขท้าย 2 ตัว</td>
                      <td
                        v-for="(prize, index) in prizes.lastTwoDigits"
                        :key="index"
                      >
                        {{ prize }}
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </v-flex>
        </div>
        <div class="pa-10">
          <h2>ตรวจรางวัลล็อตเตอรี่ Diversition</h2>
          <div class="pa-5">
            <input
              v-model="userNumber"
              placeholder="กรอกหมายเลขของคุณ"
              style="
                border: 2px solid black;
                border-radius: 4px;
                padding: 8px;
                outline: none;
                margin-right: 20px;
              "
            />
            <v-btn
              dense
              color="#FFCC99"
              style="border-radius: 36px"
              @click="checkPrize"
            >
              ตรวจรางวัล
            </v-btn>
          </div>
          <p v-if="resultMessage" class="result-message">{{ resultMessage }}</p>
        </div>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
export default {
  data () {
    return {
      userNumber: '',
      prizesGenerated: false,
      prizes: {
        first: null,
        second: [],
        nearby: [],
        lastTwoDigits: []
      },
      resultMessage: ''
    }
  },
  methods: {
    generatePrizes () {
      // สุ่มรางวัลที่ 1
      this.prizes.first = this.randomNumber(900) + 100

      // สุ่มรางวัลที่ 2
      this.prizes.second = this.generateUniqueNumbers(900, 3).map(
        (num) => num + 100
      )

      // สุ่มเลขข้างเคียงรางวัลที่ 1
      this.prizes.nearby = [this.prizes.first - 1, this.prizes.first + 1].map(
        (num) => (num < 100 ? 999 : num >= 1000 ? 0 : num)
      )

      // สุ่มรางวัลเลขท้าย 2 ตัว
      this.prizes.lastTwoDigits = this.generateUniqueNumbers(100, 1).map(
        (num) => num.toString().padStart(2, '0')
      )

      this.prizesGenerated = true
    },
    generateUniqueNumbers (max, count) {
      const numbers = new Set()
      while (numbers.size < count) {
        numbers.add(this.randomNumber(max))
      }
      return Array.from(numbers)
    },
    randomNumber (max) {
      return Math.floor(Math.random() * max)
    },
    checkPrize () {
      const number = this.userNumber.padStart(3, '0')

      if (number === this.prizes.first.toString().padStart(3, '0')) {
        this.resultMessage = 'คุณถูกรางวัลที่ 1!'
      } else if (this.prizes.second.map(num => num.toString().padStart(3, '0')).includes(number)) {
        this.resultMessage = 'คุณถูกรางวัลที่ 2!'
      } else if (this.prizes.nearby.map(num => num.toString().padStart(3, '0')).includes(number)) {
        this.resultMessage = 'คุณถูกรางวัลเลขข้างเคียงรางวัลที่ 1!'
      } else if (this.prizes.lastTwoDigits.includes(this.userNumber.slice(-2).padStart(2, '0'))) {
        this.resultMessage = 'คุณถูกรางวัลเลขท้าย 2 ตัว!'
      } else {
        this.resultMessage = 'ขออภัย คุณไม่ถูกรางวัล'
      }
    }
  }
}
</script>

<style scoped>
.prizes-table {
  width: 100%;
  border-collapse: collapse;
  margin: 0 auto;
}

.prizes-table th,
.prizes-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: center;
}

.prizes-table th {
  background-color: #f4f4f4;
}

.prizes-table tr:nth-child(even) {
  background-color: #f9f9f9;
}

.prizes-table tr:hover {
  background-color: #f1f1f1;
}

.result-message {
  font-size: 20px;
  font-weight: bold;
  color: #ff6600;
  background-color: #fff4e6;
  border: 1px solid #ff6600;
  border-radius: 5px;
  padding: 10px;
  text-align: center;
  margin-top: 10px;
  animation: fadeIn 1s ease-in-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>
