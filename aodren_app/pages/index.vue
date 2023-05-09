<template>
  <div>
    <div class="top-layer">
      <div class="logo">
        <img src="../logo.png" alt="Logo">
      </div>
      <div class="main-title">
        <h1>Suivi Stock de Surplus</h1>
      </div>
      <div class="clock">
        {{ currentTime }}
      </div>
    </div>
  <div class="container">
    <div class="left-section">
      <h2>Stock</h2>
      <form>
      <div>
        <label for="produit">Produit:</label>
        <select id="produit" v-model="selectedProduit" @change="fetchTableauData">
          <option v-for="produit in produits" :value="produit.id" :key="produit.id">{{ produit.nom }}</option>
        </select>
      </div>
    </form>
      <div>
        <table>
        <tbody>
          <tr>
            <th>Pièce</th>
            <th>Quantité en Stock</th>
          </tr>
          <tr v-for="(row, index) in tableauData" :key="index">
            <td>{{ row.nom }}</td>
            <td>{{ row.stock }}</td>
          </tr>
        </tbody>
        </table>
      </div>
    </div>
    <div class="right-section">
      <h2>Formulaire</h2>
      <form>
        <div class="form-row">
          <div class="radio-container">
            <label for="prise">Prise</label>
            <input type="radio" value="prise" v-model="selectedValue" required>
          </div>
          <div class="radio-container">
            <label for="prise">Mise</label>
            <input type="radio" value="mise" v-model="selectedValue" required>
          </div>
        </div>

        <div v-if="selectedValue == 'prise'" class="form-row" id="cause-row">
          <label for="cause">Cause :</label>
          <select id="cause" v-model="selectedCause">
            <option v-for="cause in causes" :value="cause.nom" :key="cause.id">{{ cause.nom }}</option>
          </select>
        </div>


        <div class="form-row">
          <label for="date">Date:</label>
          <input type="date" id="date"  :value="getCurrentDate()" required>
        </div>


        <div class="form-row" id="pole-row">
          <label for="pole">Pôle :</label>
          <select id="pole" v-model="selectedPole">
            <option v-for="pole in poles" :value="pole.nom" :key="pole.id">{{ pole.nom }}</option>
          </select>
        </div>

        <div class="form-row" id="produit-row">
          <label for="produit">Produit:</label>
          <select id="produit" v-model="selectedProduit2" @change="fetchTableauData2">
            <option v-for="produit in produits" :value="produit.id" :key="produit.id">{{ produit.nom }}</option>
          </select>
        </div>

        <div class="form-row" id="piece-row">
          <label for="piece">Piece:</label>
          <select id="piece" v-model="selectedPiece">
            <option v-for="piece in tableauData2" :value="piece.id" :key="piece.id">{{ piece.nom }}</option>
          </select>
        </div>

        <div class="form-row">
          <label for="quantity">Quantité:</label>
          <input type="number" v-model="quantiteSelected" id="quantity" required>
        </div>

        <div class="form-row">
          <input type="submit" value="Enregistrer" @click="Submit">
          <button type="reset" class="reset-button">Effacer</button>
        </div>
      </form>
    </div>
  </div>
    </div>
</template>

<script>


export default {

  mounted() {
    this.updateCurrentTime();
    setInterval(() => {
      this.updateCurrentTime();
    }, 1000);
  },


  async fetch() {
    try {
      const response = await fetch('http://localhost:5000/getproduits');
      if (response.ok) {
        const data = await response.json();
        this.produits = data;
      } else {
        throw new Error('Erreur lors de la requête HTTP');
      }
    } catch (error) {
      console.error(error);
    }
  },

  data() {
    return {
      date: undefined,
      currentTime: '',
      quantiteSelected: undefined,
      selectedProduit: '',
      selectedPiece: undefined,
      selectedValue: 'mise',
      selectedPole: '',
      selectedCause: '',
      selectedProduit2: '',
      produits: [],
      causes: [{ id: 1, nom: "KO" }, { id: 2, nom: "Manque Train" }, { id: 3, nom: "Autre" }],
      poles: [{ id: 1, nom: "Coloration" }, { id: 2, nom: "Coupe" }, { id: 3, nom: "Embossage/Filetage" }, { id: 4, nom: "Encollage / Rembordage" }, { id: 5, nom: "Marquage" }, { id: 6, nom: "Parage / Refente" }, { id: 7, nom: "Piquage Main" }, { id: 8, nom: "Préguttage / Guttage" }, { id: 9, nom: "Surcoupe" }],
      tableauData: [],
      tableauData2: [],
    };
  },
  methods: {


    getCurrentDate() {
    const today = new Date();
    const year = today.getFullYear();
    let month = today.getMonth() + 1;
    let day = today.getDate();

    // Formatage du mois et du jour sur deux chiffres
    if (month < 10) {
      month = '0' + month;
    }
    if (day < 10) {
      day = '0' + day;
    }

    return `${year}-${month}-${day}`;
  },



    updateCurrentTime() {
      const now = new Date();
      const hours = String(now.getHours()).padStart(2, "0");
      const minutes = String(now.getMinutes()).padStart(2, "0");
      const seconds = String(now.getSeconds()).padStart(2, "0");
      this.currentTime = `${hours}:${minutes}:${seconds}`;
    },
    async fetchTableauData() {
      try {
        const response = await fetch(`http://localhost:5000/getproduit/${this.selectedProduit}`);
        if (response.ok) {
          const data = await response.json();
          this.tableauData = data;
        } else {
          throw new Error('Erreur lors de la requête HTTP');
        }
      } catch (error) {
        console.error(error);
      }
    },
    async fetchTableauData2() {
      try {
        const response = await fetch(`http://localhost:5000/getproduit/${this.selectedProduit2}`);
        if (response.ok) {
          const data = await response.json();
          this.tableauData2 = data;
          console.log(this.tableauData2);
        } else {
          throw new Error('Erreur lors de la requête HTTP');
        }
      } catch (error) {
        console.error(error);
      }
    },
    async Submit() {
        if(this.selectedValue == "mise")
        {
            await this.AddMise()
        }
        else if (this.selectedValue == "prise")
        {
            await this.AddPrise()
        }
        else
        {
          console.log("ERROR")
        }
        
    },
    async AddMise() {
      
      fetch('http://localhost:5000/addmise', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          date: this.date,
          pole: this.selectedPole,
          id_piece: this.selectedPiece,
          quantite: this.quantiteSelected,
        }),
      })

    },
    async AddPrise() {
      fetch('http://localhost:5000/addprise', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          date: this.date,
          pole: this.selectedPole,
          cause : this.selectedCause,
          id_piece: this.selectedPiece,
          quantite: this.quantiteSelected,
        }),
      })

    }
  },


}

</script>

<style>
.top-layer {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100px;
  display: flex;
  padding-top: 5px;
  padding-bottom: 10px;
  background-color: rgb(195, 195, 195);
  border-bottom: 3px solid black;
}


h2 {
  font-size: 38px;
}


.logo {
  height: 50px;
  padding-left: 30px;
  padding-top: 10px;
}

.clock {
  padding-right: 30px;
  padding-top: 22px;
  font-size: 42px;
  font-family: Arial, Helvetica, sans-serif;
}

.main-title {
  padding-top: 10px;
  flex-grow: 1;
  text-align: center;
  font-size: 28px;
  justify-content: center;
}

body {
  font-family: Arial, sans-serif;
  overflow: hidden;
  /* Empêche les barres de défilement */
  margin: 0;
  /* Supprime la marge par défaut du corps */
  padding: 0;
  /* Supprime le remplissage par défaut du corps */
  text-align: center;
  background-color: rgba(127, 255, 212, 0);
  height: 100%;
}


input[type="submit"] {
  padding: 10px 20px;
  font-size: 18px;
  background-color: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
}

button[type="reset"] {
  padding: 10px 20px;
  font-size: 18px;
  background-color: #af574c;
  color: white;
  border: none;
  cursor: pointer;
}

button[type="reset"]:hover {
  background-color: #8f372b;
}

input[type="submit"]:hover {
  background-color: #45a049;
}

.container {
  display: flex;
  flex-direction: line;
}

.left-section,
.right-section {
  flex: 1;
  padding-bottom: 0px;
  border: #000000;
  height: 900px;
}

label {
  font-size: 18px;
}

.left-section {
  background-color: #4ea4af00;
  padding: 20px;
  padding-top: 125px;
  border-right: 1px solid black;
}

.right-section {
  background-color: #7ec0a100;
  padding: 20px;
  padding-top: 125px;
  border-left: 1px solid black;
}


/* Styling du formulaire */
form {
  margin: 0 auto;
  font-size: 24px;
  font-family: Arial, Helvetica, sans-serif;
  max-width: 700px;
  margin-top: 30px;
}

form input,
form select,
form textarea {
  margin-bottom: 50px;
}


input[type="date"],
select,
input[type="radio"] {
  margin-bottom: 6px;
  width: 100%;
  padding: 6px;
  font-size: 16px;
}


.radio-container {
  display: inline-block;
  margin-right: 10px;
  /* Facultatif : ajoute un espacement entre les boutons */
}

table {
  margin: 0 auto;
  font-family: Arial, Helvetica, sans-serif;
  text-align: center;
  max-width: 700px;
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  border: 1px solid black;
  padding: 8px;
}

th {
  background-color: gray;
  color: white;
}
</style>

