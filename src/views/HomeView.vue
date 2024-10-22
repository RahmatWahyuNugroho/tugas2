<template>
  <div class="home">
    <h1>Penilaian Karyawan</h1>

    <div class="form-group">
      <label for="name">Nama Karyawan:</label>
      <select class="option" v-model="selectedName">
        <option disabled value="">Pilih karyawan</option>
        <option v-for="name in names" :key="name" :value="name">{{ name }}</option>
      </select>  
    </div>

    <table class="penilaian-table">
      <thead>
        <tr>
          <th>No</th>
          <th>Kriteria Penilaian</th>
          <th>Nilai (1-5)</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(score, index) in ratings" :key="index">
          <td>{{ index + 1 }}</td>
          <td>{{ score.criteria }}</td>
          <td><input type="number" v-model.number="score.value" min="1" max="5" @input="checkScore(index)" /></td>
        </tr>
      </tbody>
    </table>

    <div class="button-group">
      <button @click="clearForm" class="btn">Reset</button>
      <button @click="preview" class="btn">Pratinjau</button>
    </div>

    <div v-if="showPreview" class="preview" id="print-area">
      <div class="info">
        <p><strong>Nama :</strong> {{ selectedName }}</p>
        <p><strong>Posisi :</strong> Developer</p>
        <p><strong>Alamat :</strong> Jl.Soekarno Hatta, No. A17, Lowokwaru-Malang</p>
        <br>
        <p><strong>Daftar Penilaian</strong></p>
      </div>

      <ul class="rating-list">
        <li v-for="(score, index) in ratings" :key="index" class="rating-item">
          <div>
            {{ index + 1 }}. {{ score.criteria }}
          </div>
          <div class="stars">
            {{ displayStars(score.value) }}
          </div>
        </li>
      </ul>

      <div class="total-score">
        <p><strong>Total Persentase:</strong> {{ percentage }}%</p>
        <p><strong>Grade:</strong> {{ grade }}</p>
      </div>
    </div>

    <button v-if="showPreview" @click="downloadPDF" class="btn">Download</button>
  </div>
</template>

<script>
import jsPDF from 'jspdf';
import html2canvas from 'html2canvas';

export default {
  data() {
    return {
      selectedName: '',
      names: ['Fikri Haikal', 'Eko Wahyudi', 'Wahyu Nugroho'],
      ratings: [
        { criteria: 'Mengerti tentang PHP', value: null },
        { criteria: 'Mengerti tentang HTML & JavaScript', value: null },
        { criteria: 'Pernah menggunakan database', value: null },
        { criteria: 'Paham OOP', value: null },
        { criteria: 'Paham dasar Git', value: null },
        { criteria: 'Pengalaman menggunakan framework web', value: null },
        { criteria: 'Mampu bekerja dalam tim', value: null },
        { criteria: 'Lancar berbahasa Inggris', value: null }
      ],
      showPreview: false,
    };
  },
  computed: {
    totalScore() {
      return this.ratings.reduce((total, score) => total + (score.value || 0), 0);
    },
    percentage() {
      const maxScore = this.ratings.length * 5;
      return Math.round((this.totalScore / maxScore) * 100);
    },
    grade() {
      const percent = this.percentage;
      if (percent >= 90) return 'A';
      if (percent >= 70) return 'B';
      if (percent >= 50) return 'C';
      if (percent >= 25) return 'D';
      if (percent >= 6) return 'E';
      return 'F';
    }
  },
  methods: {
    clearForm() {
      this.selectedName = '';
      this.ratings.forEach(score => (score.value = null));
      this.showPreview = false;
    },
    preview() {
      const allFilled = this.ratings.every(score => score.value !== null);
      if (this.selectedName && allFilled) {
        this.showPreview = true;
      } else {
        alert('Mohon isi semua inputan sebelum melihat pratinjau');
      }
    },
    checkScore(index) {
      if (this.ratings[index].value < 1) this.ratings[index].value = 1;
      if (this.ratings[index].value > 5) this.ratings[index].value = 5;
    },
    displayStars(value) {
      const filledStar = '\u2605'; // Bintang terisi
      const emptyStar = '\u2606';  // Bintang kosong
      return filledStar.repeat(value) + emptyStar.repeat(5 - value);
    },
    downloadPDF() {
      const printArea = document.getElementById('print-area');
      html2canvas(printArea, { scale: 2 }).then(canvas => {
        const imgData = canvas.toDataURL('image/png');
        const pdf = new jsPDF({
          orientation: 'portrait',
          unit: 'mm',
          format: 'a4'
        });

        const imgWidth = 210;
        const imgHeight = (canvas.height * imgWidth) / canvas.width;
        const pageHeight = 297;
        let position = 0;

        pdf.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);

        if (imgHeight >= pageHeight) {
          let remainingHeight = imgHeight;
          while (remainingHeight >= pageHeight) {
            pdf.addPage();
            pdf.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);
            remainingHeight -= pageHeight;
          }
        }

        pdf.save('penilaian-karyawan.pdf');
      });
    }
  }
};
</script>

<style scoped>
.home {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  font-family: Arial, sans-serif;
  border: 1px solid #ccc;
  border-radius: 8px;
}
.form-group {
  flex-direction: column;
  display: flex;
  margin-bottom: 20px;
}

.form-group label {
  display: flex;
}

.option {
  max-width: 200px; /* Atur lebar maksimum dropdown */
  width: 100%; /* Pastikan dropdown mengisi lebar maksimal yang ditentukan */
  padding: 8px; /* Padding untuk dropdown */
}

h1 {
  text-align: center;
  color: #555;
  font-size: 24px;
}

.penilaian-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.penilaian-table th,
.penilaian-table td {
  border: 1px solid #aaa;
  padding: 10px;
  text-align: left;
}

.button-group {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;
}

.btn {
  padding: 10px 20px;
  background-color: #4caf50; /* Ganti warna hijau */
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-left: 10px;
}

.btn:hover {
  background-color: #45a049; /* Warna saat hover */
}

.preview {
  margin-top: 20px;
  background-color: #fff;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  text-align: left; /* Perataan kiri untuk pratinjau */
}

.info {
  margin-bottom: 20px;
}

.rating-list {
  list-style-type: none;
  padding: 0;
}

.rating-item {
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
}

.stars {
  margin-top: 5px;
  color: orange; /* Ubah warna bintang */
  font-size: 20px;
}

.total-score {
  margin-top: 20px;
  text-align: right; /* Perataan kanan untuk total persentase dan grade */
}

.total-score p {
  font-size: 18px;
  font-weight: bold;
}

@media print {
  body {
    margin: 0;
    padding: 0;
  }

  #print-area {
    width: 210mm; 
    height: 297mm; 
    padding: 20mm; 
    box-sizing: border-box;
    text-align: left; /* Perataan kiri untuk cetakan */
  }

  .btn {
    display: none; 
  }
}
</style>
