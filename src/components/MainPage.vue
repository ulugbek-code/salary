<template>
  <div class="m">
    <h1>Калькулятор зарплаты</h1>
    <EachDay
      v-for="(each, i) in allSalary"
      :per="each"
      :index="i"
      :key="each"
      @deleting="cancelEach"
      @calculated="getTime"
    />
    <div class="addBtn">
      <button @click="adding">Добавить день</button>
    </div>
  </div>
  <div class="answers">
    <h1>Общий</h1>
    <h3>
      Рабочее время: <span>{{ totalHours ? totalHours : "00ч00" }}</span>
    </h3>
    <h3>
      Зарплата:
      <span
        >{{
          !isNaN(calculatedSalary) ? convertSalary(calculatedSalary) : "00.0"
        }}
        сум</span
      >
    </h3>
  </div>
  <div class="daySalary">
    <h3>Дневная зарплата</h3>
    <input
      :class="{ 'bg-red': notSalary }"
      v-model="salary"
      @input="convert()"
      type="text"
      :placeholder="notSalary ? 'нет цены' : '... ...'"
    />
    <span>сум</span>
    <button @click="restart">
      <svg
        viewBox="0 0 16 16"
        class="bi bi-arrow-repeat"
        fill="currentColor"
        height="16"
        width="16"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d="M11.534 7h3.932a.25.25 0 0 1 .192.41l-1.966 2.36a.25.25 0 0 1-.384 0l-1.966-2.36a.25.25 0 0 1 .192-.41zm-11 2h3.932a.25.25 0 0 0 .192-.41L2.692 6.23a.25.25 0 0 0-.384 0L.342 8.59A.25.25 0 0 0 .534 9z"
        ></path>
        <path
          d="M8 3c-1.552 0-2.94.707-3.857 1.818a.5.5 0 1 1-.771-.636A6.002 6.002 0 0 1 13.917 7H12.9A5.002 5.002 0 0 0 8 3zM3.1 9a5.002 5.002 0 0 0 8.757 2.182.5.5 0 1 1 .771.636A6.002 6.002 0 0 1 2.083 9H3.1z"
          fill-rule="evenodd"
        ></path>
      </svg>
      Перезапуск
    </button>
  </div>
</template>

<script>
import EachDay from "@/components/EachDay.vue";
export default {
  components: {
    EachDay,
  },
  data() {
    return {
      notSalary: false,
      salary: null,
      allSalary: [Date.now()],
      times: [],
      totalHours: null,
    };
  },
  computed: {
    removedSalary() {
      return this.salary?.replace(/\s/g, "");
    },
    calculatedSalary() {
      let minutes = this.totalHours?.replace(/(.*)ч/, "");
      let hours = this.totalHours?.split("ч")[0];
      minutes = Number(minutes) / 60;

      let all = (
        (Number(hours) + minutes) *
        (Number(this.removedSalary) / 9)
      ).toFixed(2);
      return all;
    },
  },
  methods: {
    restart() {
      (this.notSalary = false),
        (this.salary = null),
        (this.allSalary = [Date.now()]),
        (this.times = []),
        (this.totalHours = null);
    },
    convert() {
      if (isNaN(this.salary[0])) this.salary = "";
      this.salary = this.salary.replaceAll(" ", "");
      let x = Number(this.salary);
      this.salary = x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, " ");
      if (this.salary < 0) this.salary = Math.abs(this.salary);
    },
    convertSalary(val) {
      if (isNaN(val[0])) val = "";
      val = val.replaceAll(" ", "");
      let x = Number(val);
      val = x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, " ");
      if (val < 0) val = Math.abs(val);
      return val;
    },
    adding() {
      this.allSalary[this.allSalary.length] = Date.now();
    },
    cancelEach(val) {
      this.allSalary = this.allSalary.filter((each) => each != val);
      this.times = this.times.filter((each) => each.id != val);
    },
    getTime(val) {
      if (!this.salary) {
        this.notSalary = true;
        // console.log(this.notSalary);
        return;
      }
      this.notSalary = false;
      let a = this.times.filter((each) => each.id == val.id);
      if (a.length) {
        this.times = this.times.filter((each) => each.id != val.id);
      }
      this.times.push(val);
    },
  },
  watch: {
    times: {
      handler(val) {
        this.totalHours = val
          .reduce(
            function (memo, t) {
              // sum the times
              memo[0] += parseInt(t.h, 10);
              memo[1] += parseInt(t.m, 10);
              if (memo[1] >= 60) {
                memo[0] += ~~(memo[1] / 60);
                memo[1] = memo[1] % 60;
              }
              return memo;
            },
            [0, 0]
          )
          .map(function (t) {
            // map to the desired format
            if (t < 10) {
              return "0" + t;
            }
            return t;
          })
          .join("ч");
      },
      deep: true,
    },
  },
};
</script>

<style scoped>
.answers {
  position: fixed;
  left: 1%;
  top: 2%;
}
.daySalary {
  position: fixed;
  right: 1%;
  top: 2%;
}
.m {
  padding: 4rem 0 2rem;
}
.m h1 {
  text-align: center;
  margin-bottom: 1rem;
}
.addBtn {
  text-align: center;
  margin-top: 1rem;
}
.addBtn button {
  align-items: center;
  appearance: none;
  background-color: #fcfcfd;
  border-radius: 4px;
  border-width: 0;
  box-shadow: rgba(45, 35, 66, 0.4) 0 2px 4px,
    rgba(45, 35, 66, 0.3) 0 7px 13px -3px, #d6d6e7 0 -3px 0 inset;
  box-sizing: border-box;
  color: #36395a;
  cursor: pointer;
  display: inline-flex;
  font-family: "JetBrains Mono", monospace;
  height: 48px;
  justify-content: center;
  line-height: 1;
  list-style: none;
  overflow: hidden;
  padding-left: 16px;
  padding-right: 16px;
  position: relative;
  text-align: left;
  text-decoration: none;
  transition: box-shadow 0.15s, transform 0.15s;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  white-space: nowrap;
  will-change: box-shadow, transform;
  font-size: 18px;
}

.addBtn button:focus {
  box-shadow: #d6d6e7 0 0 0 1.5px inset, rgba(45, 35, 66, 0.4) 0 2px 4px,
    rgba(45, 35, 66, 0.3) 0 7px 13px -3px, #d6d6e7 0 -3px 0 inset;
}

.addBtn button:hover {
  box-shadow: rgba(45, 35, 66, 0.4) 0 4px 8px,
    rgba(45, 35, 66, 0.3) 0 7px 13px -3px, #d6d6e7 0 -3px 0 inset;
  transform: translateY(-2px);
}

.addBtn button:active {
  box-shadow: #d6d6e7 0 3px 7px inset;
  transform: translateY(2px);
}
.answers span {
  font-weight: 600;
  font-size: 1.3rem;
}
.daySalary h3 {
  margin-bottom: 0.3rem;
}
.daySalary input {
  background-color: white;
  display: inline-block;
  border-radius: 5px;
  border: none;
  width: 200px;
  height: 2rem;
  padding-left: 8px;
  font-size: 1.2rem;
  font-weight: 500;
  margin-right: 1rem;
  outline-color: #53afc9;
}
.daySalary input.bg-red {
  border: 2px solid #cb2027;
}
.daySalary input.bg-red::placeholder {
  color: #cb2027;
}
.daySalary span {
  position: absolute;
  font-size: 1.2rem;
  right: 7%;
  bottom: 0%;
  background: #d6d6e7;
  height: 2rem;
  padding: 0 8px;
  line-height: 2rem;
  border-radius: 0 5px 5px 0;
}
.daySalary button {
  position: absolute;
  left: 0;
  bottom: -100%;
  color: white;
  background-color: #222;
  font-weight: 500;
  border-radius: 0.5rem;
  font-size: 1rem;
  /* line-height: 2rem; */
  /* padding-left: 2rem;
  padding-right: 2rem; */
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  text-align: center;
  margin-right: 0.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
  border: none;
  cursor: pointer;
  width: 200px;
}
.daySalary button:hover {
  background-color: #333;
}

.daySalary button svg {
  display: inline;
  width: 1.3rem;
  height: 1.3rem;
  margin-right: 0.75rem;
  color: white;
}

.daySalary button:active svg {
  animation: spin_357 0.5s linear;
}

@keyframes spin_357 {
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
}
</style>
