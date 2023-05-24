<template>
  <div class="each">
    <h2>День {{ index + 1 }}</h2>
    <div class="form-input">
      <input :id="'start' + per" value="" placeholder="00:00" maxlength="5" />
      <input :id="'end' + per" value="" placeholder="00:00" />
      <button @click="diff">Рассчитать</button>
    </div>
    <div class="del">
      <button @click="$emit('deleting', per)">Удалить</button>
    </div>
  </div>
</template>

<script>
export default {
  props: ["per", "index"],
  emits: ["deleting", "calculated"],
  data() {
    return {
      value: null,
    };
  },
  methods: {
    diff() {
      let start = document.getElementById("start" + this.per)?.value;
      let end = document.getElementById("end" + this.per)?.value;
      if (!start && !end) return;
      if (!start.includes(":") || !end.includes(":")) return;

      start = start.split(":");
      end = end.split(":");
      let startDate = new Date(0, 0, 0, start[0], start[1], 0);
      let endDate = new Date(0, 0, 0, end[0], end[1], 0);
      let diff = endDate.getTime() - startDate.getTime();
      let hours = Math.floor(diff / 1000 / 60 / 60);
      diff -= hours * 1000 * 60 * 60;
      let minutes = Math.floor(diff / 1000 / 60);

      let answer =
        (hours < 9 ? "0" : "") +
        hours +
        ":" +
        (minutes < 9 ? "0" : "") +
        minutes;

      let totalArray = answer.split(":");
      this.$emit("calculated", {
        h: Number(totalArray[0]),
        m: Number(totalArray[1]),
        id: this.per,
      });
      this.value = answer;
    },
  },
};
</script>

<style scoped>
.each {
  width: 60%;
  margin: 0 auto;
  display: flex;
  justify-content: center;
  /* align-items: center; */
  padding: 0.8rem 0;
}
.each h2 {
  text-align: center;
  color: #fff;
  margin-right: 2rem;
  margin-top: 5px;
}
.form-input {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.form-input input {
  background-color: white;
  display: inline-block;
  border-radius: 5px;
  border: none;
  width: 70px;
  height: 2rem;
  padding-left: 8px;
  font-size: 1.2rem;
  font-weight: 500;
  margin-right: 1rem;
  outline-color: #589db0;
}
.form-input button {
  background-color: #27ae60;
  border: 1px solid transparent;
  border-radius: 3px;
  box-shadow: rgba(255, 255, 255, 0.4) 0 1px 0 0 inset;
  box-sizing: border-box;
  color: #fff;
  cursor: pointer;
  font-size: 1rem;
  margin: 0;
  outline: none;
  padding: 8px 0.8em;
  position: relative;
  text-align: center;
  text-decoration: none;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  vertical-align: baseline;
  white-space: nowrap;
}

.form-input button:hover,
.form-input button:focus {
  background-color: #14ce62;
}

.form-input button:focus {
  box-shadow: 0 0 0 4px #27ae5f3f;
}

.form-input button:active {
  background-color: #27ae60;
  box-shadow: none;
}
.del {
  margin-left: 4rem;
}
.del button {
  background-color: #cb2027;
  border: 1px solid transparent;
  border-radius: 3px;
  box-shadow: rgba(255, 255, 255, 0.4) 0 1px 0 0 inset;
  box-sizing: border-box;
  color: #fff;
  cursor: pointer;
  font-size: 1rem;
  margin: 0;
  outline: none;
  padding: 8px 0.8em;
  position: relative;
  text-align: center;
  text-decoration: none;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  vertical-align: baseline;
  white-space: nowrap;
}

.del button:hover,
.del button:focus {
  background-color: #d64b4f;
}

.del button:focus {
  box-shadow: 0 0 0 4px #cb202649;
}

.del button:active {
  background-color: #cb2027;
  box-shadow: none;
}
</style>
