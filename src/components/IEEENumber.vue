<template>
  <div>
    <h1>IEEE-754 Floating-Point Conversion</h1>
    <section class="decimal-value">
      <h2>Decimal Value</h2>
      <input v-model="value" />
    </section>
    <section class="ieee-bits">
      <h2>IEEE 754 bits</h2>
      <span :key="i" v-for="(bit, i) of bits">
        <span class="hidden-bit" v-if="i === 12">
          1.
        </span>
        <span class="bit-wrapper">
          <div class="bit-index">{{ 63 - i }}</div>
          <input
              type="number"
              min="0"
              max="1"
              :class="i === 0 ? 'sign-bit' : i <= 11 ? 'exponent-bit' : 'fraction-bit'"
              v-model="bits[i]"
          />
        </span>
      </span>
    </section>
  </div>
</template>

<script>
export default {
  name: 'IEEENumber',
  data: () => ({
    value: 0,
    bits: Array(64).fill(0)
  }),
  watch: {
    value: function() {
      this.valueToBits();
    },
    bits: function() {
      this.bitsToValue();
    }
  },
  methods: {
    valueToBits: function() {
      let uint8View = new Uint8Array(8);
      let float64View = new Float64Array(uint8View.buffer);

      float64View[0] = Number(this.value);

      console.log('value change -------------');
      console.log(uint8View, float64View);

      for (let i = 0; i < 8; i++) {
        let byte = uint8View[7 - i];

        for (let j = 0; j < 8; j++) {
          this.bits[i * 8 + 7 - j] = byte & 1;
          byte = byte >> 1;
        }
      }

      console.log(this.bits.join(''));
    },
    bitsToValue: function() {
      let uint8View = new Uint8Array(8);
      let float64View = new Float64Array(uint8View.buffer);

      for (let i = 0; i < 8; i++) {
        let byte = 0;

        for (let j = 0; j < 8; j++) {
          byte = byte << 1;
          byte |= Number(this.bits[i * 8 + j]);
        }

        uint8View[7 - i] = byte;
      }

      console.log(uint8View, float64View);
      this.value = float64View[0];
    }
  }
};
</script>

<style scoped>
body, input {
  font-size: 16px;
}

input {
  text-align: center;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 2px;
}

.ieee-bits input {
  width: 1em;
  height: 1.5em;
  margin: 2px;
  padding: 0;
}

input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type=number] {
  -moz-appearance: textfield;
}

.bit-wrapper {
  display: inline-block;
}

.bit-index {
  font-size: 0.8em;
}

.sign-bit {
  color: red;
}

.exponent-bit {
  color: blue;
}

.fraction-bit {
  color: black;
}
</style>
