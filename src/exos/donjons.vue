<script setup>
import { dungeons } from './data/Data_donjons';
import { ref, computed } from 'vue';

const stasisValues = ref(dungeons.map(() => 0));

// Calcul des valeurs multipliées et du total
const multipliedValues = computed(() => {
  return dungeons.map((donjon, index) => {
    return (Number(stasisValues.value[index]) || 0) * donjon.nombre_joueurs;
  });
});

const totalCoffres = computed(() => {
  return multipliedValues.value.reduce((sum, current) => sum + current, 0);
});

function updateStasis(index, value) {
  stasisValues.value[index] = Number(value) || 0,10;
}
</script>

<template>
  <div class="content">
    Informations sur les Donjons :
    <br/>
    <table class="tablea">
      <thead>
        <tr>
          <th class="tableau">#</th>
          <th class="tableau">Nom du Donjon</th>
          <th class="tableau">Level Minimum</th>
          <th class="tableau">Nb joueurs</th>
          <th class="tableau">Stasis</th>
          <th class="tableau">Nombre de coffres</th>
          <th class="tableau">Stratégie</th>

        </tr>
      </thead>
      <tbody>
        <tr v-for="(donjon, index) of dungeons" :key="index">
          <td class="tableau">{{ index + 1 }}</td>
          <td class="tableau">{{ donjon.name }}</td>
          <td class="tableau">{{ donjon.level }}</td>
          <td class="tableau">{{ donjon.nombre_joueurs }}</td>
          <td class="tableau">
            <input 
              type="number" 
              :value="stasisValues[index]" 
              @input="updateStasis(index, $event.target.value)"
              class="stasis-input"
              min="0"
              max="10"
            />
          </td>
          <td class="tableau">{{ multipliedValues[index] }}</td>
          <td class="tableau">{{ donjon.strategie }}</td>
        </tr>   
      </tbody>
      <tfoot>
        <tr>
          <td colspan="5" class="tableau total-label">Total Coffres :</td>
          <td class="tableau total-value">{{ totalCoffres }}</td>
        </tr>
      </tfoot>
    </table>
  </div>
</template>

<style scoped>
.tableau {
  border: 2px solid;
  padding: 10px;
}

.tablea {
  border-collapse: collapse;
  width: 100%;
}

.content {
  padding-left: 40px;
}

.stasis-input {
  width: 60px;
  text-align: center;
}

.total-label {
  font-weight: bold;
  text-align: right;
}

.total-value {
  font-weight: bold;
}
</style>