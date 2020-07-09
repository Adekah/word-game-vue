<template>
  <div id="app" class="container mt-4">
    <div class="card mb-4" v-if="tamamlandi">
      <div class="card-body">Tebrikler yarışmayı {{this.puan}} puan ile tamamladınız.</div>
    </div>

    <div class="card mt-4" v-if="!mevcutSoru">
      <div class="card-header">
        <h5 class="mb-0">Kelime Oyununa Hoşgeldiniz.</h5>
      </div>
      <div class="card-body">Yarışmaya başlamak için başla butonuna basın</div>
      <div class="card-footer">
        <button class="btn btn-primary" @click="basla">Yarışmaya başla</button>
      </div>
    </div>
    <div class="card mt-4" v-else>
      <h3>
        <div class="card-header">{{mevcutSoru.soru}}</div>
      </h3>
      <div class="card-body">
        <div class="d-flex">
          <harf :deger="harf.harf" :acik="harf.acildi" v-for="(harf, index) in harfler" :key="'harf-'+index"></harf>
        </div>
      </div>
      <div class="card-footer">
        <div class="d-flex">
          <div class="mr-4">Toplam Puan : {{puan}}</div>
          <div class="mr-4">
            Kalan Süreniz:
            <kbd>{{this.kalanSure}}</kbd>
          </div>
          <div class="mr-4">Harf Punanı : {{harfPuan}}</div>
        </div>
      </div>
      <div class="card-footer">
        <div class="input-group">
          <input
            class="form-control"
            type="text"
            placeholder="Cevap"
            v-model="yarismaciCevap"
            @keyup="yarismaciCevap=yarismaciCevap.toLocaleUpperCase('tr')"
          />
          <div class="input-group-append">
            <button class="btn btn-primary mr-2" @click="harfver">Harf İste</button>
            <button @click="cevapver" class="btn btn-success">Cevap ver</button>
          </div>
        </div>
      </div>
      <div class="card-footer" :class="mesajClass" v-if="mesaj">{{mesaj}}</div>
    </div>
  </div>
</template>

<script>
//Componenti Sadece burada kullanacaksam buradan ekliyorum aksi halde main.js'e ekliyorum
import Harf from "@/components/Harf";
export default {
  name: "App",
  components: {
    Harf
  },
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
      mesaj: null,
      mesajClass: "",
      mesajSure: 0,
      mevcutSoru: null,
      harfler: [],
      puan: 0,
      harfPuan: 0,
      yarismaciCevap: "",
      sure: null,
      kalanSure: 240
    };
  },
  methods: {
    basla() {
      this.tamamlandi = false;
      this.puan = 0;
      this.sorular.map(x => {
        x.soruldu = false;
      });
      this.kalanSure = 240;
      this.sure = setInterval(() => {
        this.kalanSure--;
        if (this.kalanSure === 0) {
          this.bitir();
        }
      }, 1000);

      this.soruver();
      this.mesajgoster("İyi yarışmalar");
    },
    bitir() {
      clearInterval(this.sure);
      this.mevcutSoru = null;
      this.tamamlandi = true;
    },
    mesajgoster(mesaj, tur) {
      this.mesaj = mesaj;
      if (tur === "hata") {
        this.mesajClass = "bg-danger text-white";
      } else if (tur === "basari") {
        this.mesajClass = "bg-success   text-white";
      } else {
        this.mesajClass = "bg-dark text-white";
      }
      if (this.mesajSure) {
        clearTimeout(this.mesajSure);
        this.mesajSure = null;
      }
      this.mesajSure = setTimeout(() => (this.mesaj = null), 3000);
    },
    soruver() {
      this.yarismaciCevap = "";
      this.mevcutSoru = this.sorular.find(x => !x.soruldu);
      if (!this.mevcutSoru) {
        this.bitir();
        return;
      }
      this.harfler = [];
      this.mevcutSoru.cevap.split("").map(x => {
        this.harfler.push({
          harf: x,
          acildi: false
        });
      });
      this.harfPuan = this.harfler.length * 100;
      this.mevcutSoru.soruldu = true;
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
      if (!this.yarismaciCevap.length) {
        return;
      }

      if (this.yarismaciCevap.length !== this.harfler.length) {
        this.mesajgoster("Harf sayıları Uyumlu değil", "hata");
        return;
      }

      // let cevap = this.yarismaciCevap .replace(/ı/, "I") .replace(/i/, "İ")  .toLocaleUpperCase(); -->> burada türkçe karater için replace edilebilir ya da toLocaleUpperCase('tr') kullanılır
      let cevap = this.yarismaciCevap.toLocaleUpperCase("tr");
      this.yarismaciCevap = cevap;

      if (
        this.yarismaciCevap === this.mevcutSoru.cevap.toLocaleUpperCase("tr")
      ) {
        this.puan += this.harfPuan;
        this.mesajgoster("Tebrikler Doğru Bildiniz", "basari");
      } else {
        this.puan -= this.harfPuan;
        this.mesajgoster(
          `Yanlış bildiniz Doğru cevap ${this.mevcutSoru.cevap}!`,
          "hata"
        );
      }

      this.soruver();
    }
  }
};
</script>

<style>
</style>
