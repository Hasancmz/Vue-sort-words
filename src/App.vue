<template>
  <div id="app" class="container mt-4">
    <div class="card mb-5" v-if="tamamlandi">
      <div class="card-body">Tebrikler yarışmayı {{ this.puan }} puanla bitirdiniz.</div>
    </div>
    <div class="card" v-if="!mevcutSoru">
      <div class="card-header">
        <h5>Rastgele verilmiş harflerden doğru kelimeyi bulunuz</h5>
      </div>
      <div
        class="card-body"
      >İpucunu öğrenmek sorunun puan değerini 300 azaltır. Yarışmaya başlamak için yarışmaya başla butonuna basınız.</div>
      <div class="card-footer">
        <button class="btn btn-primary" @click="basla">Yarışmaya başla</button>
      </div>
    </div>
    <div class="card" v-else>
      <div class="card-header d-flex align-items-center">
        <div class="mr-3">
          <h5>{{ siradakiSoru }}/{{ toplamSoru }}</h5>
        </div>
        <h4>
          İpucu :
          <span>{{ ipucu }}</span>
        </h4>
      </div>
      <div class="card-body">
        <div class="d-flex">
          <Harf :deger="harf" v-for="(harf, index) in randomHarf" :key="'harf-' + index" />
        </div>
      </div>
      <div class="card-footer d-flex align-items-center justify-content-around">
        <div class="mr-5">Harf Puan : {{ harfPuan }}</div>
        <div class="mr-5">Toplam Puan : {{ puan }}</div>
        <div class="time">
          Kalan süreniz :
          <kbd>{{ kalanSure }}</kbd>
        </div>
        <div class="ml-5">
          <button class="btn btn-primary" @click="anahtar">İPUCU</button>
        </div>
      </div>
      <div id="mobile" class="card-footer d-flex align-items-center justify-content-around">
        <div>
          Kalan süreniz :
          <kbd>{{ kalanSure }}</kbd>
        </div>
        <div>
          <button class="btn btn-primary" @click="anahtar">İPUCU</button>
        </div>
      </div>
      <div class="card-footer">
        <div class="input-group">
          <input
            class="form-control"
            v-model="yarismaciCevap"
            @keyup="yarismaciCevap = yarismaciCevap.toLocaleUpperCase('tr')"
            type="text"
            placeholder="Cevabınızı yazın"
          />
          <div class="input-group-append">
            <button class="btn btn-success" @click="cevapver">Cevap Ver</button>
            <button class="btn btn-warning" @click="pas">Pas</button>
          </div>
        </div>
      </div>
      <div class="card-footer text-center" :class="mesajClass" v-if="mesaj">
        <h6 class="mb-0">{{ mesaj }}</h6>
      </div>
    </div>
  </div>
</template>

<script>
import Harf from "@/components/Harf";

export default {
  name: "App",
  components: {
    Harf,
  },
  data() {
    return {
      kelimeler: [
        {
          numara: 1,
          kelime: "KAN",
          ipucu: "Kırmızı renginde bir sıvı",
          soruldu: false,
        },
        {
          numara: 2,
          kelime: "HAVA",
          ipucu: "Çeşitli gazlardan meydana gelmiş bir karışım",
          soruldu: false,
        },
        {
          numara: 3,
          kelime: "TÜRLÜ",
          ipucu: "Kompleks yapıdaki sebze yemeği",
          soruldu: false,
        },
        {
          numara: 4,
          kelime: "SALEP",
          ipucu: "Orkide çiçeğinden yapılan kışın çokca tüketilen içecek",
          soruldu: false,
        },
        {
          numara: 5,
          kelime: "AVANAK",
          ipucu:
            "Asıl anlamı “sıpa” olup günümüzde “kolaylıkla kandırılabilen kimse” anlamında kullanılan söz",
          soruldu: false,
        },
        {
          numara: 6,
          kelime: "HADEME",
          ipucu: "İş yerlerinde temizlik ve getir götür işlerine bakan görevli",
          soruldu: false,
        },
        {
          numara: 7,
          kelime: "ECZANE",
          ipucu: "İlaçları tedarik ettiğimiz yer",
          soruldu: false,
        },

        {
          numara: 8,
          kelime: "DAĞINIK",
          ipucu: "Düzenli kelimesinin zıt (karşıt) anlamı",
          soruldu: false,
        },
        {
          numara: 9,
          kelime: "AKVARYUM",
          ipucu:
            "Çoğunlukla cam ya da yüksek dirençli plastik gibi saydam malzemelerden yapılır",
          soruldu: false,
        },
        {
          numara: 10,
          kelime: "KEKEMELİK",
          ipucu:
            " Kelimeleri birden söyleyemeyip baş seslerini birkaç defa tekrarlayarak güçlükle söyleme hastalığıdır",
          soruldu: false,
        },
        {
          numara: 11,
          kelime: "ADAPTASYON",
          ipucu: "Bir canlının ortama uyum sağlaması",
          soruldu: false,
        },
      ],
      mevcutSoru: null,
      harfler: [],
      randomHarf: [],
      harfPuan: 0,
      puan: 0,
      yarismaciCevap: "",
      ipucu: "",
      sure: null,
      kalanSure: 0,
      mesaj: null,
      mesajSure: null,
      tamamlandi: false,
      toplamSoru: null,
      siradakiSoru: null,
    };
  },
  methods: {
    basla() {
      this.tamamlandi = false;
      this.mevcutSoru = null;
      this.toplamSoru = this.kelimeler.length;
      this.puan = 0;
      this.kelimeler.map((x) => {
        x.soruldu = false;
      });
      this.kalanSure = 500;
      this.sure = setInterval(() => {
        this.kalanSure--;
        if (this.kalanSure === 0) {
          this.bitir();
        }
      }, 1000);

      this.kelimever();
    },
    bitir() {
      clearInterval(this.sure);
      this.mevcutSoru = null;
      this.tamamlandi = true;
    },
    kelimever() {
      this.ipucu = "";
      this.yarismaciCevap = "";
      this.mevcutSoru = this.kelimeler.find((x) => !x.soruldu);

      if (!this.mevcutSoru) {
        this.bitir();
        return;
      }

      this.siradakiSoru = this.mevcutSoru.numara;

      this.randomHarf = [];
      this.harfler = [];
      this.mevcutSoru.kelime.split("").map((x) => {
        this.harfler.push({
          harf: x,
          acildi: false,
        });
      });

      for (let i = 0; i < this.harfler.length; i++) {
        let rastgeleHarfIndex = Math.floor(Math.random() * this.harfler.length);
        let harfsira = this.harfler[rastgeleHarfIndex];

        while (harfsira.acildi) {
          let rastgeleHarfIndex = Math.floor(
            Math.random() * this.harfler.length
          );
          harfsira = this.harfler[rastgeleHarfIndex];
        }
        harfsira.acildi = true;
        this.randomHarf.push(harfsira.harf);
      }

      this.harfPuan = this.harfler.length * 100;
      this.mevcutSoru.soruldu = true;
    },
    anahtar() {
      if (this.ipucu) {
        return;
      }
      this.ipucu = this.mevcutSoru.ipucu;
      this.harfPuan -= 300;
    },
    cevapver() {
      if (this.yarismaciCevap === "") {
        this.mesajgoster("Bir cevap vermek zorundasınız");
        return;
      }

      if (this.yarismaciCevap.length !== this.mevcutSoru.kelime.length) {
        this.mesajgoster("Cevabınız toplam harf sayısı ile uyuşmamaktadır.");
        return;
      }

      //let cevap = this.yarismaciCevap.replace(/ı/,'I').replace(/i/, 'İ').toLocaleUpperCase();
      let cevap = this.yarismaciCevap.toLocaleUpperCase("tr");
      this.yarismaciCevap = cevap;

      if (
        this.yarismaciCevap === this.mevcutSoru.kelime.toLocaleUpperCase("tr")
      ) {
        this.puan += this.harfPuan;
        this.mesajgoster("Tebrikler, doğru bildiniz", "basari");
      } else {
        this.puan -= this.harfPuan;
        this.mesajgoster(
          `Malesef yanlış! Doğru Cevap '${this.mevcutSoru.kelime}'`,
          "hata"
        );
      }
      this.kelimever();
    },
    mesajgoster(mesaj, tur) {
      if (this.mesajSure) {
        clearTimeout(this.mesajSure);
        this.mesajSure = null;
      }

      this.mesaj = mesaj;

      if (tur === "hata") {
        this.mesajClass = "bg-danger text-white";
      } else if (tur === "basari") {
        this.mesajClass = "bg-success text-white";
      } else {
        this.mesajClass = "bg-dark text-white";
      }

      this.mesajSure = setTimeout(() => {
        this.mesaj = null;
      }, 3000);
    },
    pas() {
      this.kelimever();
    },
  },
};
</script>

<style scoped lang="scss">
#app {
  .card {
    #mobile {
      display: none !important;
    }
  }
}

@media screen and (max-width: 500px) {
  #app {
    .card {
      .card-header {
        h4 {
          font-size: 16px;
          span {
            font-weight: 400;
            font-size: 15px;
          }
        }
      }
      .card-footer {
        font-size: 14px;
        .mr-5 {
          margin-right: 0 !important;
        }
        .time {
          display: none;
        }
        .ml-5 {
          display: none !important;
        }
        h6 {
          font-size: 14px;
        }
      }
      #mobile {
        display: flex !important;
      }
    }
  }
}
</style>
