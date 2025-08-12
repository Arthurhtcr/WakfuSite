<template>
  <div class="metier">
    <form @submit.prevent="calculate">
      <div>
        <label for="actLvl">Niveau actuel : </label>
        <input id="actLvl" v-model.number="actLvl" type="number">
      </div>
      <br/>
      <div>
        <label for="newLvl">Niveau visé : </label>
        <input id="newLvl" v-model.number="newLvl" type="number">
      </div>
      <br/>
      <div>
        <label for="actXp">Expérience actuelle :</label>
        <input id="actXp" v-model.number="actXp" type="number">
      </div>
      <br/>
      <button type="submit">Calculer</button>
    </form>
    <br/>
    <div id="result">{{ result }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      actLvl: 0,
      newLvl: 0,
      actXp: 0,
      result: ''
    }
  },
  methods: {
    calculate() {
      const nextXp = this.actLvl * 150 + 75;
      
      if (this.actXp >= nextXp) {
        this.result = "Exp actuelle impossible...";
      } else if (this.actLvl > this.newLvl) {
        this.result = "Niveau actuel supérieur  au niveau visé.";
      } else if (this.actLvl === this.newLvl) {
        this.result = `Vous avez déjà le level ${this.newLvl}.`;
      } else {
        const neededXp = this.xpNeed(this.actLvl, this.newLvl, this.actXp);
        this.result = `Il faut ${neededXp} d'exp pour atteindre le lvl ${this.newLvl}.`;
      }
    },
    totXp(lvl) {
      return (0.5 * lvl * (lvl - 1) * 150 + lvl * 75);
    },
    xpNeed(oldLvl, newLvl, currentXp) {
      return (this.totXp(newLvl) - this.totXp(oldLvl) - currentXp);
    }
  }
}
</script>

<style scoped>
.metier{
  display: flex;
  margin-left: 40px;
  margin-top: 40px;
  text-align: center;
  padding: 5px;
  border: solid 3px;
  border-radius: 5px;
  border-color: rgb(85, 85, 85);
}
</style>