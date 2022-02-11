<script lang="ts">
import { computed, defineComponent, Ref, ref } from 'vue';
import axios from 'axios';

// interface ResponseApi {
//   payment_channel: string;
//   payment_channel_detail: string;
//   full_id: string;
//   name: string;
// }

export default defineComponent({
  setup() {
    const data = ref<any>({});
    axios
      .get('https://api.dev.sekolah.mu/se-test/invoice', {
        headers: {
          Authorization: 'secret_auth_token!!$$',
        },
      })
      .then((res) => {
        console.log(res.data.data.data);
        data.value = res.data.data.data;
      });
    // const dateOfPurchase = computed(() => data.full_id ?? 'asd');
    return {
      data,
      // dateOfPurchase,
    };
  },
  computed: {
    dateOfPurchase() {
      return (
        new Date(this.data.created_at).toLocaleDateString('id-ID', {
          day: 'numeric',
          month: 'long',
          year: 'numeric',
        }) ?? 'asd'
      );
    },
    toRupiah() {
      return (value: string) => {
        return new Intl.NumberFormat('id-ID', {
          style: 'currency',
          currency: 'IDR',
        }).format(+value);
      };
    },
    subTotal() {
      return this.data.items[0].programs.reduce((acc, curr) => {
        return acc + +curr.amount;
      }, 0);
    },
  },
});
</script>

<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png" /> -->
  <main class="content">
    <section class="group detail">
      <h4 class="heading-text">Detail Pembelian</h4>
      <div class="detail-pembelian">
        <span id="attribute">No. Tagihan</span>
        <span>{{ data.full_id }} </span>
        <span id="attribute">Nama Pengguna</span><span> {{ data.name }} </span>
        <span id="attribute">Tanggal Pembelian</span
        ><span> {{ dateOfPurchase }} </span>
      </div>
    </section>
    <section class="group pembayaran">
      <h4 class="heading-text">Pembayaran Berhasil</h4>
      <div class="payment-date-info">
        <span>Tanggal Pembayaran</span><span>{{ 'tangggal pem' }}</span>
      </div>
      <div class="payment-detail">
        <div id="payment-detail-top">
          <h5>Metode Pembayaran</h5>
        </div>
        <div>
          <span>{{ data.payment_channel }} </span>
          <span>{{ data.payment_channel_detail }} </span>
        </div>
      </div>
    </section>
    <section class="group program">
      <h4 class="heading-text">Detail Program</h4>
      <span>Penyedia Program</span>
      <div class="program-wrapper">
        <template v-for="program in data.items[0].programs" :key="program">
          <span>{{ program.details.name }}</span>
          <span>{{ toRupiah(program.amount) }}</span>
        </template>
        <div class="program-subtotal">
          <span>Subtotal</span>
          <span>{{ toRupiah(subTotal) }} </span>
        </div>
      </div>
    </section>
    <section class="group payment-summary">
      <span>Total Harga</span><span> {{ toRupiah(data.total_amount) }} </span>
      <span>Potongan Harga({{ data.voucher.voucher_code }}) </span>
      <span> -{{ toRupiah(data.voucher.voucher_amount) }} </span>
      <span>Total</span
      ><span>
        {{ toRupiah(+data.total_amount - +data.voucher.voucher_amount) }}
      </span>
    </section>
    <section class="group footer">
      <span>Kontak Kami</span>
      <span>0813-8811-1756(WA)</span>
      <span>suaramu@sekolahmu.co.id</span>
    </section>
  </main>
</template>

<style lang="scss">
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

.content {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  padding: 0.5rem;
}

.group {
  font-size: 13px;
}

.heading-text {
  font-size: 700;
  padding-bottom: 0.5rem;
  border-top: 0;
  border-left: 0;
  border-right: 0;
  border-bottom: 1px;
  border-color: grey;
  border-style: solid;
}

.detail-pembelian {
  display: grid;
  grid-template-columns: 1fr max-content;
  font-size: 12px;

  #attribute {
    display: flex;
    position: relative;
    width: 100%;
    // background-color: red;
    &::after {
      content: ':';
      // align-self: flex-end;
      justify-self: flex-end;
      // display: absolute;
    }
  }
}
.pembayaran {
  display: flex;
  flex-direction: column;
  gap: 1rem;

  .payment-summary {
    display: flex;
    flex-direction: row;
    // justify-content: space-between;
    gap: 0.5rem;
  }

  .payment-detail {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    border: 1px solid grey;
    border-radius: 8px;

    #payment-detail-top {
      background-color: #dfe4e9;
      padding: 0.5rem 0.3rem;
      widows: 100%;
      h5 {
        // background-color: #2c3e50;
      }
    }

    div {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
      padding: 0.5rem 0.3rem;
    }
  }
}

.program-wrapper {
  display: grid;
  grid-template-columns: auto auto;
  gap: 0.5rem;
  border: 1px solid grey;
  border-radius: 8px;
  padding: 1rem;
  font-weight: 500;

  > :nth-child(odd) {
    color: rgb(31, 117, 216);
  }
}

.program-subtotal {
  grid-column: 1 / 3;
  display: flex;
  justify-content: space-between;
  padding: 0.7rem 0 0 0;
  align-items: center;
  border-top: 1px solid grey;

  > :nth-child(odd) {
    color: black;
    font-weight: 600;
  }
}

.payment-summary {
  display: grid;
  grid-template-columns: 1fr 1fr;
  border-top: 1px solid grey;
  border-bottom: 1px solid grey;
  padding: 1rem 0;
  gap: 0.5rem;

  > :nth-child(even) {
    text-align: end;
  }
}

.footer {
  display: flex;
  flex-direction: column;
  // position: fixed;
  // bottom: 0;
  // margin-bottom: 40px;
}
</style>
