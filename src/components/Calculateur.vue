<template>
  <div class="container">
    <h1>Comptes Tanto</h1>


    <div v-for="(restaurant, key) in restaurants" :key="key" class="restaurant">
      <h3>{{ key }}</h3>
      <input type="text" v-model="restaurant.montantBrut" placeholder="Montant brut" @input="formatInput(key)" />
      <p>Net: {{ calculerRevenuNet(restaurant.montantBrut, restaurant.coefficient).toFixed(2) }} €</p>
    </div>

    <div class="summary">
      <p>Total Net : <strong>{{ totalRevenuNet.toFixed(2) }} €</strong></p>
    </div>


    <div class="inputs">
      <div class="input-group">
        <label>Montant AE</label>
        <input type="text" v-model="montantAE" placeholder="Montant AE" @input="formatInput('montantAE')" />
      </div>
      <div class="input-group">
        <label>Total Mensuel Veille</label>
        <input type="text" v-model="montantMensuelVeille" placeholder="Total Mensuel Veille" @input="ajouterAuMensuel" />
      </div>
    </div>

    <div class="summary">
      <p>Total Mensuel : <strong>{{ totalMensuelFinal.toFixed(2) }} €</strong></p>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue';

export default {
  setup() {
    // Initialisation des restaurants avec montants à ''
    const restaurants = ref({
      "Uber Tanto Bene": { montantBrut: '', coefficient: 0.64 },
      "Deliveroo Tanto Bene": { montantBrut: '', coefficient: 0.664 },
      "Uber Green Wave": { montantBrut: '', coefficient: 0.64 },
      "Un Air de Toscane": { montantBrut: '', coefficient: 0.64 },
      "Uber Sweet & Co": { montantBrut: '', coefficient: 0.64 },
      "Deliveroo Sweet & Co": { montantBrut: '', coefficient: 0.664 },
      "Uber Waffle": { montantBrut: '', coefficient: 0.64 },
      "Deliveroo Waffle": { montantBrut: '', coefficient: 0.664 }
    });

    // Champs Montant AE et Total Mensuel Veille initialisés à des chaînes vides
    const montantAE = ref('');
    const montantMensuelVeille = ref('');

    // Calcul du revenu net en fonction du coefficient
    const calculerRevenuNet = (montant, coef) => {
      return parseFloat(montant) * coef || 0; // Assure que le résultat soit un nombre, même si la valeur est vide
    };

    // Calcul du total net
    const totalRevenuNet = computed(() => {
      return Object.values(restaurants.value).reduce((acc, restaurant) => {
        return acc + (restaurant.montantBrut ? calculerRevenuNet(restaurant.montantBrut, restaurant.coefficient) : 0);
      }, 0);
    });

    // Calcul du Total Mensuel : Mensuel veille + Total net du jour + AE
    const totalMensuelFinal = computed(() => {
      return (parseFloat(montantMensuelVeille.value) || 0) + totalRevenuNet.value + (parseFloat(montantAE.value) || 0);
    });

    // Fonction pour formater les entrées et les convertir en nombre avec un point décimal
    const formatInput = (field) => {
      let value;
      if (field === 'montantAE') {
        value = montantAE.value;
      } else if (field === 'montantMensuelVeille') {
        value = montantMensuelVeille.value;
      }

      if (value !== '' && !isNaN(parseFloat(value))) {
        // Remplacer la virgule par un point et convertir en nombre
        value = value.replace(',', '.');
        if (field === 'montantAE') {
          montantAE.value = value;
        } else if (field === 'montantMensuelVeille') {
          montantMensuelVeille.value = value;
        }
      } else {
        // Si la valeur n'est pas valide, réinitialiser
        if (field === 'montantAE') {
          montantAE.value = '';
        } else if (field === 'montantMensuelVeille') {
          montantMensuelVeille.value = '';
        }
      }
    };

    // Ajouter le montant Mensuel Veille au total (aucune modification dans ce calcul)
    const ajouterAuMensuel = () => {
      // Ne fait rien ici maintenant car totalMensuelFinal fait ce calcul automatiquement.
    };

    return {
      restaurants,
      montantAE,
      montantMensuelVeille,
      totalMensuelFinal,
      totalRevenuNet,
      calculerRevenuNet,
      ajouterAuMensuel,
      formatInput
    };
  }
};
</script>

<style>
.container {
  max-width: 400px;
  margin: auto;
  padding: 15px;
  text-align: center;
  background: #145d5d; /* Couleur cyan */
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
input {
  width: 100%;
  padding: 8px;
  margin: 5px 0;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 5px;
}
.restaurant {
  padding: 10px 0;
  border-bottom: 1px solid #ddd;
}
.inputs {
  margin-bottom: 15px;
}
.input-group {
  margin-bottom: 10px;
  text-align: left;
}
.input-group label {
  display: block;
  font-weight: bold;
  margin-bottom: 5px;
}
.summary {
  margin-top: 20px;
  font-size: 16px;
  font-weight: bold;
}
</style>
