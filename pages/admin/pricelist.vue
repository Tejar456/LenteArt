<template>
  <div class="container">
    <div class="kiri">
      <div class="form">
        <form @submit.prevent="tambahPackage">
          <div class="form-group">
            <label>Nama Paket</label>
            <input v-model="form.judul" type="text" class="input-select" />
          </div>

          <div class="form-group">
            <label>Tier</label>
            <select v-model="form.tier" class="input-select">
              <option v-for="(item, i) in kategori" :key="i" :value="item.id">
                {{ item.tier }}
              </option>
            </select>
          </div>

          <div class="form-group">
            <label>Package</label>
            <select v-model="form.package" class="input-select">
              <option value="">Pilih Ukuran</option>
              <option value="Engagement">Engagement</option>
              <option value="Prewedding">Prewedding</option>
              <option value="Wedding">Wedding</option>
              <option value="Spesial">Spesial</option>
              <option value="Video">Video</option>
            </select>
          </div>

          <div class="form-group">
            <label>Harga</label>
            <input v-model="form.price" type="text" class="input-select" />
          </div>

          <div class="form-group">
            <label>Benefit</label>
            <textarea v-model="form.benefit" type="text" class="input-select" />
          </div>

          <div class="form-group">
            <label>Output</label>
            <textarea v-model="form.output" type="text" class="input-select" />
          </div>
          <button type="submit" class="btn-submit">Tambah Paket</button>
        </form>
      </div>
    </div>

    <div class="kanan">
      <div class="pricelist">
        <div class="grid-container">
          <div v-for="(list, i) in pricelist" :key="i">
            <div class="card-container">
              <div class="card">
                <p class="title">{{ list.judul }} {{ list.kategori.tier }}</p>
                <p class="harga">Rp{{ list.price }}</p>
                <p class="benefit">
                  {{ list.benefit }}
                </p>
                <p class="output">
                  {{ list.output }}
                </p>
                <button @click="Update(list.id)">Edit</button>
                <button @click="hapusPackage(list.id)">Hapus</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  layout: "admin",
  middleware: "auth",
});
const supabase = useSupabaseClient();
const pricelist = ref([]);
const kategori = ref([]);
const form = ref({
  judul: "",
  tier: "",
  package: "",
  price: "",
  benefit: "",
  output: "",
});

const tambahPackage = async () => {
  const { error } = await supabase.from("package").insert([form.value]);
  console.log(form.value);
  if (!error) window.location.reload();
};

const getKategori = async () => {
  const { data, error } = await supabase.from("kategori").select("*");
  if (data) kategori.value = data;
};

const getData = async () => {
  const { data } = await supabase.from("package").select(`*, kategori(*)`);
  if (data) pricelist.value = data;
};

const Update = async (id) => {
  const newPrice = prompt("Masukkan harga baru:");

  if (newPrice && !isNaN(newPrice)) {
    const { data, error } = await supabase.from("package").update({ price: newPrice }).eq("id", id);

    if (error) {
      alert("Terjadi kesalahan saat memperbarui data: " + error.message);
    } else {
      alert("Harga berhasil diperbarui!");
      getData();
    }
  } else {
    alert("Harga tidak valid.");
  }
};

const hapusPackage = async (packageId) => {
  const { error } = await supabase.from("package").delete().eq("id", packageId);
  if (!error) {
    window.location.reload();
  }
};

onMounted(() => {
  getData();
  getKategori();
});
</script>

<style scoped>
.container {
  display: grid;
  height: 100vh;
  grid-template-columns: repeat(2, 1fr);
}

.form {
  display: flex;
  flex-direction: column;
  width: 100%;
  margin: 20px 0px 20px 20px;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  height: 90vh;
}

.pricelist {
  margin: 20px;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  overflow-y: auto;
  height: 90vh;
}

::-webkit-scrollbar {
  display: none;
}

.form-group {
  margin-bottom: 15px;
}

label {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 5px;
  display: block;
  color: #333;
}

.input-file,
.input-select {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 10px;
  font-size: 16px;
  color: #333;
  background-color: #ff0000;
}

.input-file {
  background-color: #fff;
  width: 18, 1rem;
}

.input-select {
  background-color: #f0f0f0;
  width: 20rem;
}

.btn-submit {
  padding: 10px 20px;
  background-color: #3f3f3f;
  color: #fff;
  font-size: 16px;
  font-weight: 600;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn-submit:hover {
  background-color: #e8e8e8;
  color: #000;
}

.btn-submit:active {
  background-color: #e8e8e8;
  color: #000;
}

.judul > h1 {
  text-align: center;
  margin-top: 100px;
}

.grid-container {
  display: grid;
  grid-template-columns: auto auto auto;
  padding: 10px;
}

.card-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  width: 100%;
}

.card {
  width: 300px;
  height: auto;
  border-radius: 12px;
  font-family: sans-serif;
  background-color: #fff;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 10px rgba(0, 0, 0, 0.24);
  transition: all 300ms;
  padding: 20px;
  margin: 20px;
}

.card .harga {
  font-size: 1em;
  font-weight: 600;
  color: #000;
  margin-bottom: 4px;
}

.card .title {
  font-size: 1.2em;
  font-weight: 600;
  color: #000;
}

button {
  background-color: #dfdfdf;
  border: none;
  border-radius: 8px;
  padding: 10px 20px;
  margin: 10px;
  cursor: pointer;
}

.benefit {
  white-space: pre-line;
}
</style>
