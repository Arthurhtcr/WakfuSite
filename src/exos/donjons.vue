<script setup>
import { dungeons } from './data/Data_donjons';
import { ref, computed, onMounted } from 'vue';

// 1. Récupère les valeurs sauvegardées ou initialise à 0
const savedValues = JSON.parse(localStorage.getItem('wakfuStasisValues')) || [];
const stasisValues = ref(dungeons.map((_, index) => savedValues[index] || 0));

// 2. Sauvegarde automatique quand les valeurs changent
function updateStasis(index, value) {
  const numValue = Math.min(Math.max(Number(value) || 0, 0), 10); // Force entre 0 et 10
  stasisValues.value[index] = numValue;
  
  // Sauvegarde dans localStorage
  localStorage.setItem('wakfuStasisValues', JSON.stringify(stasisValues.value));
}

// Calculs existants (inchangés)
const multipliedValues = computed(() => {
  return dungeons.map((donjon, index) => {
    return stasisValues.value[index] * donjon.nombre_joueurs;
  });
});

const totalCoffres = computed(() => {
  return multipliedValues.value.reduce((sum, current) => sum + current, 0);
});


function resetStasis() {
  stasisValues.value = dungeons.map(() => 0);
  localStorage.removeItem('wakfuStasisValues');
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
    <button @click="resetStasis" class="reset-btn">Réinitialiser</button>
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


.reset-btn {
  /* Style de base */
  padding: 10px 20px;
  margin: 15px 0;
  background: linear-gradient(135deg, #00bd7e, #008b5d);
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  font-size: 16px;

  /* Effets interactifs */
  &:hover {
    background: linear-gradient(135deg, #00a76f, #007c53);
    transform: translateY(-2px);
    box-shadow: 0 6px 8px rgba(0, 0, 0, 0.15);
  }

  &:active {
    transform: translateY(1px);
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
}
</style>