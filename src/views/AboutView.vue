<template>
  <div class="about">
    <div class="container">
      <h1>Form Pengajuan Izin Cuti</h1>
      <div class="form-card">
        <form @submit.prevent="handleSubmit" class="form">
          <div class="form-group">
            <label for="name">Nama:</label>
            <input type="text" id="name" v-model="form.name" required />
          </div>

          <div class="form-group">
            <label for="jeniscuti">Jenis Cuti:</label>
            <select id="jeniscuti" v-model="form.jeniscuti" required>
              <option disabled value="">Pilih Jenis Cuti</option>
              <option value="Cuti Tahunan">Cuti Tahunan</option>
              <option value="Cuti Sakit">Cuti Sakit</option>
              <option value="Cuti Melahirkan">Cuti Melahirkan</option>
              <option value="Cuti Besar">Cuti Besar</option>
              <option value="Cuti Alasan Penting">Cuti Alasan Penting</option>
            </select>
          </div>

          <div class="form-group">
            <label for="berapahari">Jumlah Hari:</label>
            <input type="number" id="berapahari" v-model="form.berapahari" required />
          </div>

          <div class="form-group">
            <label for="awalcuti">Tanggal Mulai:</label>
            <input type="date" id="awalcuti" v-model="form.awalcuti" required />
          </div>

          <div class="form-group">
            <label for="akhircuti">Tanggal Selesai:</label>
            <input type="date" id="akhircuti" v-model="form.akhircuti" required />
          </div>

          <div class="form-group">
            <label for="alasan">Alasan:</label>
            <textarea id="alasan" v-model="form.alasan" required></textarea>
          </div>

          <div class="form-group">
            <button @click.prevent="download" class="btn">Download PDF</button>
          </div>

        </form>
      </div>
    </div>
  </div>
</template>

<script>
import jsPDF from 'jspdf';

export default {
  data() {
    return {
      form: {
        name: '',
        jeniscuti: '',
        berapahari: '',
        awalcuti: '',
        akhircuti: '',
        alasan: ''
      },
    };
  },
  methods: {
    download() {
      // Validasi form
      for (const key in this.form) {
        if (!this.form[key]) {
          alert('Semua kolom harus diisi. Harap lakukan peninjauan kembali.');
          return;
        }
      }

      const doc = new jsPDF();
      const currentDate = new Date();
      const formattedDate = currentDate.toLocaleDateString('id-ID', {
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      });

      doc.setFontSize(14);
      doc.text('SURAT IZIN CUTI', 105, 20, { align: 'center' }); // Centered title

      doc.setFontSize(12);
      doc.text('Kepada Yth:', 20, 40);
      doc.text('Pimpinan Sumber Daya Manusia', 20, 50); // Placeholder for company name
      doc.text('Di Tempat', 20, 60);
      
      doc.text('Yang bertanda tangan di bawah ini:', 20, 80);
      doc.text(`Nama: ${this.form.name}`, 20, 90);
      doc.text(`Jenis Cuti: ${this.form.jeniscuti}`, 20, 100);
      doc.text(`Jumlah Hari: ${this.form.berapahari}`, 20, 110);
      doc.text(`Tanggal Mulai: ${this.form.awalcuti}`, 20, 120);
      doc.text(`Tanggal Selesai: ${this.form.akhircuti}`, 20, 130);
      doc.text(`Alasan: ${this.form.alasan}`, 20, 140);
      
      doc.text('Demikian surat izin cuti ini saya buat dengan sebenar-benarnya.', 20, 160);
      doc.text('Saya berharap izin ini dapat disetujui.', 20, 170);
      doc.text(`Malang, ${formattedDate}`, 20, 190);
      doc.text('Hormat Saya,', 20, 200);
      doc.text(`(${this.form.name})`, 20, 230);

      doc.save('Surat-Izin-Cuti.pdf');
    }
  }
};
</script>

<style scoped>
/* Basic styling for the container */
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #e9f7ef; /* Warna latar belakang baru */
  border-radius: 8px;
  box-shadow: 0 2px 15px rgba(0, 0, 0, 0.2);
}

/* Title styling */
h1 {
  text-align: center;
  color: #2c3e50; /* Warna judul baru */
  margin-bottom: 20px;
}

/* Styling for form card */
.form-card {
  background: #ffffff; /* Latar belakang kartu */
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

/* Simple styling for the form */
.form {
  margin-top: 20px;
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  font-weight: bold;
  margin-bottom: 5px;
}

input, textarea, select {
  width: 100%;
  padding: 10px;
  font-size: 14px;
  border: 1px solid #00ff08; /* Border warna hijau baru */
  border-radius: 4px;
  transition: border-color 0.3s;
}

input:focus, textarea:focus, select:focus {
  border-color: #f46302; /* Fokus border hijau lebih gelap */
  outline: none;
}

textarea {
  resize: vertical;
}

.btn {
  padding: 10px 15px;
  background-color: #06a32a; /* Tombol warna hijau */
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
  width: 100%;
}

.btn:hover {
  background-color: #536809; /* Hover tombol hijau lebih gelap */
}
</style>
