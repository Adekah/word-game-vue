<template>
  <div id="app" class="container">
    <div class="card mt-4" v-if="!mevcutSoru">
      <div class="card-body">Yarışmaya başlamak için başla butonuna basın</div>
      <div class="card-footer">
        <button class="btn btn-primary" @click="basla">Yarışmaya başla</button>
      </div>
    </div>
    <div class="card mt-4" v-else>
      <div class="card-header">{{mevcutSoru.soru}}</div>
      <div class="card-body">
        <div class="d-flex">
          <div class="harf shadow mr-3" v-for="(harf, index) in harfler" :key="'harf-'+index">
            <span v-if="harf.acildi">{{harf.harf}}</span>
            <span v-else></span>
          </div>
        </div>
      </div>
      <div class="card-footer">Harf Punanı : {{harfPuan}}</div>
      <div class="card-footer">Toplam Puan : {{puan}}</div>
      <div class="card-footer">
        <p>
          <input
            class="form-control"
            type="text"
            placeholder="cevaplarınızı yaın"
            v-model="yarismaciCevap"
            @keyup='yarismaciCevap=yarismaciCevap.toLocaleUpperCase("tr")'
          />
        </p>
        <p>Cevabınız : {{yarismaciCevap}}</p>
        <button @click="cevapver">cevap ver</button>
      </div>
      <div class="card-footer">
        <button class="btn btn-secondary" @click="harfver">harf ver</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      sorular: [
        {
          soru: "Siyah ile aynı anlama gelen bir renk",
          cevap: "KARA",
          soruldu: false
        },
        {
          soru: "Sık kullanılan bir isim",
          cevap: "AHMET",
          soruldu: false
        },
        {
          soru: "Türkiyenin başkenti",
          cevap: "ANKARA",
          soruldu: false
        },
        {
          soru: "Karadenizde bir ilimiz",
          cevap: "TRABZON",
          soruldu: false
        }
      ],
      mevcutSoru: null,
      harfler: [],
      puan: 0,
      harfPuan: 0,
      yarismaciCevap: ""
    };
  },
  methods: {
    basla() {
      this.mevcutSoru = this.sorular.find(x => !x.soruldu);
      this.harfler = [];
      this.mevcutSoru.cevap.split("").map(x => {
        this.harfler.push({
          harf: x,
          acildi: false
        });
      });
      this.harfPuan = this.harfler.length * 100;
    },
    harfver() {
      let rastgeleHarfIndex = Math.floor(Math.random() * this.harfler.length);

      if (this.harfPuan <= 100) {
        return;
      }

      let harf = this.harfler[rastgeleHarfIndex];
      while (harf.acildi) {
        rastgeleHarfIndex = Math.floor(Math.random() * this.harfler.length);
        harf = this.harfler[rastgeleHarfIndex];
      }
      harf.acildi = true;
      this.harfPuan -= 100;
    },
    cevapver() {
      // let cevap = this.yarismaciCevap .replace(/ı/, "I") .replace(/i/, "İ")  .toLocaleUpperCase(); -->> burada türkçe karater için replace edilebilir ya da toLocaleUpperCase('tr') kullanılır
      let cevap = this.yarismaciCevap.toLocaleUpperCase("tr");
      this.yarismaciCevap = cevap;

      if (
        this.yarismaciCevap === this.mevcutSoru.cevap.toLocaleUpperCase("tr")
      ) {
        alert("tebrikler ");
      } else {
        alert("yanlıs");
      }
    }
  }
};
</script>

<style>
.harf {
  width: 100px;
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 30pt;
}
</style>
