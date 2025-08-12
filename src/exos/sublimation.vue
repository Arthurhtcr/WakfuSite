<template>
  <div class="container">
    <h1>Liste des Sublimations</h1>
    
    <!-- Barre de recherche texte -->
    <div class="search-box">
      <input
        v-model="textSearch"
        placeholder="Rechercher dans le nom ou l'effet..."
        class="search-input"
      />
    </div>
    
    <div class="filters-container">
      <!-- Cases à cocher pour type épique/relique -->
      <div class="type-filters">
        <label class="type-filter">
          <input type="checkbox" v-model="showEpic" />
          <span class="epic-marker"></span> Épique
        </label>
        <label class="type-filter">
          <input type="checkbox" v-model="showRelic" />
          <span class="relic-marker"></span> Relique
        </label>
      </div>
      
      <!-- Barre de recherche par couleur -->
      <div class="color-search">
        <div class="color-inputs">
          <div v-for="(color, index) in colorFilters" :key="index" class="color-input">
            <select v-model="colorFilters[index]" class="color-select">
              <option value="">-</option>
              <option value="rouge">Rouge</option>
              <option value="bleue">Bleue</option>
              <option value="verte">Verte</option>
              <option value="jaune">Jaune (joker)</option>
            </select>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Liste des états filtrés -->
    <div class="states-list">
      <div 
        v-for="(state, index) in filteredStates" 
        :key="index" 
        class="state-card"
        :class="{'epic-card': state.type === 'epic', 'relic-card': state.type === 'relic'}"
      >
        <div class="state-colors" v-if="state.couleur">
          <div 
            v-for="(color, i) in getColorParts(state.couleur)" 
            :key="i" 
            class="color-dot"
            :class="getColorClass(color)"
            :title="getColorName(color)"
          ></div>
        </div>
        <div class="state-info">
          <h3>{{ state.nom }}</h3>
          <p><strong>Effet:</strong> {{ state.effet }}</p>
          <p><strong>Niveau max:</strong> {{ state.niveau_max || '-' }}</p>
          <p><strong>Obtention:</strong> {{ state.obtention || state.pierre || '-' }}</p>
        </div>
      </div>
    </div>
    
    <div v-if="filteredStates.length === 0" class="no-results">
      Aucun résultat trouvé.
    </div>
  </div>
</template>

<script>
import { sublimation,sublimationsEpiques,sublimationsReliques } from './data/Data_sublimation';

export default {
  data() {
    return {
      // Fusion des deux types de données
      states: [
        ...sublimation.map(item => ({ ...item, type: 'color' })),
        ...sublimationsEpiques.map(item => ({ ...item, type: 'epic' })),
        ...sublimationsReliques.map(item => ({ ...item, type: 'relic' }))
      ],
      textSearch: '',
      colorFilters: ['', '', ''],
      showEpic: true,
      showRelic: true
    };
  },
  computed: {
    filteredStates() {
      let filtered = this.states;
      
      // Filtre par type
      filtered = filtered.filter(state => {
        if (state.type === 'epic' && !this.showEpic) return false;
        if (state.type === 'relique' && !this.showRelic) return false;
        return true;
      });
      
      // Filtre texte
      if (this.textSearch) {
        const searchTerm = this.textSearch.toLowerCase();
        filtered = filtered.filter(state => 
          state.nom.toLowerCase().includes(searchTerm) || 
          state.effet.toLowerCase().includes(searchTerm)
        );
      }
      
      // Filtre couleur (uniquement pour les items avec couleur)
      const activeColorFilters = this.colorFilters.filter(c => c);
      if (activeColorFilters.length > 0) {
        filtered = filtered.filter(state => {
          if (!state.couleur) return true; // On garde les items sans couleur
          
          const stateColors = this.getColorParts(state.couleur);
          return activeColorFilters.every((filterColor, index) => {
            if (index >= stateColors.length) return false;
            return filterColor === 'jaune' || 
                   stateColors[index].includes(filterColor);
          });
        });
      }
      
      return filtered;
    }
  },
  methods: {
    getColorParts(couleur) {
      if (!couleur) return [];
      const parts = [];
      let remaining = couleur;
      
      while (remaining.length > 0) {
        if (remaining.startsWith('rouge')) {
          parts.push('rouge');
          remaining = remaining.slice(5);
        } else if (remaining.startsWith('bleue')) {
          parts.push('bleue');
          remaining = remaining.slice(5);
        } else if (remaining.startsWith('verte')) {
          parts.push('verte');
          remaining = remaining.slice(5);
        } else {
          parts.push(remaining);
          break;
        }
      }
      
      return parts;
    },
    getColorClass(color) {
      return {
        'red': color.includes('rouge'),
        'blue': color.includes('bleue'),
        'green': color.includes('verte'),
        'yellow': color.includes('jaune')
      };
    },
    getColorName(color) {
      if (color.includes('rouge')) return 'Rouge';
      if (color.includes('bleue')) return 'Bleue';
      if (color.includes('verte')) return 'Verte';
      if (color.includes('jaune')) return 'Jaune';
      return color;
    },
  }
};
</script>

<style>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.search-box {
  margin-bottom: 20px;
}

.filters-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  margin-bottom: 20px;
}

.type-filters {
  display: flex;
  gap: 15px;
  background: #1f1f1f;
  padding: 15px;
  border-radius: 4px;
  align-items: center;
}

.type-filter {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}

.epic-marker {
  display: inline-block;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background-color: #ff00ff; /* Magenta pour épique */
}

.relic-marker {
  display: inline-block;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background-color: #9400d3; /* Violet pour relique */
}

.color-search {
  flex: 1;
  background: #1f1f1f;
  padding: 15px;
  border-radius: 4px;
}

.search-input {
  width: 100%;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.color-inputs {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}

.color-input {
  display: flex;
  align-items: center;
  gap: 5px;
}

.color-select {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}


.states-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 20px;
}

.state-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  display: flex;
  gap: 15px;
}

.epic-card {
  border: 4px solid #ff00ff;
}

.relic-card {
  border: 4px solid #9400d3;
}

.state-colors {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.color-dot {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 1px solid #333;
}

.color-dot.red {
  background-color: #ff5252;
}

.color-dot.blue {
  background-color: #4285f4;
}

.color-dot.green {
  background-color: #0f9d58;
}

.color-dot.yellow {
  background-color: #ffeb3b;
}

.state-info {
  flex: 1;
}

.state-info h3 {
  margin-top: 0;
  margin-bottom: 10px;
}

.state-info p {
  margin: 5px 0;
}

.no-results {
  text-align: center;
  padding: 20px;
  color: #666;
}

@media (max-width: 768px) {
  .filters-container {
    flex-direction: column;
  }
}
</style>