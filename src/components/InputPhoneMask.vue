<template>
  <input
      type="text"
      :value="inputFormatted"
      @paste.prevent="onPaste"
      @input="onInput"
      @keypress="onKeypress"
      :maxlength="maxLength"
  />
</template>

<script>
import {
  AsYouType,
  isValidPhoneNumber,
  parsePhoneNumberWithError,
} from 'libphonenumber-js/max'

export default {
  name: 'InputPhoneMask',
  props: {
    value: {
      type: String,
    },
  },
  emits: ['input'],
  data() {
    return {
      inputFormatted: '',
      inputRaw: '',
      selected: {},
    }
  },
  computed: {
    isFullNumber() {
      const phoneWithOneAdditionalChar = this.inputFormatted + '0'
      if (isValidPhoneNumber(phoneWithOneAdditionalChar)) return false
      return isValidPhoneNumber(this.inputFormatted)
    },
    maxLength() {
      if (this.isFullNumber) {
        return this.inputFormatted.length
      } else {
        return 17
      }
    },
  },
  mounted() {},
  watch: {
    value: {
      handler(newValue) {
        if (newValue) {
          this.inputFormatted = this.formatInput(newValue)
        }
        if (!newValue) {
          this.inputFormatted = ''
        }
      },
      immediate: true,
    },
  },
  methods: {
    onKeypress(e) {
      const inputChar = e.key
      if (!this.checkIfCharPlusOrNumber(inputChar)) {
        e.preventDefault()
        // let nextValue;
        // let preformattedValue;
        // let replacedValue;
        // if (this.selected.selectionStart || this.selected.selectionEnd) {
        //   replacedValue = this.replaceSelected(value, inputChar);
        //   preformattedValue = this.preformatInputValue(replacedValue);
        // } else {
        //   nextValue = value + inputChar;
        //   preformattedValue = this.preformatInputValue(nextValue);
        // }
        //
        // // const preformattedValue = this.preformatInputValue(replacedValue);
        // if (replacedValue) {
        //   this.formatInput(preformattedValue);
        // }
        // if (this.isFullNumber(nextValue)) {
        //   this.formatInput(preformattedValue);
        // }
        // if (!this.isFullNumber(value)) {
        //   this.formatInput(preformattedValue);
        // }
        // // if (!this.isFullNumber(nextValue)) {
        // //   this.formatInput(nextValue);
        // // }
      }
      // this.emitInput();
    },
    onPaste(e) {
      const inputPaste = e.clipboardData.getData('text/plain')
      const extractedNumbers = inputPaste.replace(/[^0-9]/g, '')
      try {
        const preparedPhoneNumber = this.preformatInputValue(extractedNumbers)
        const foundPhoneNumber = parsePhoneNumberWithError(preparedPhoneNumber)
        const number = foundPhoneNumber.number
        this.inputFormatted = this.formatInput(number)
      } catch (error) {
        this.inputFormatted = extractedNumbers
      }
      this.emitInput()
    },
    onInput(e) {
      let value = e.target.value
      if (!value) {
        this.inputFormatted = ''
        this.emitInput()
      } else {
        value = this.preformatInputValue(value)
        value = this.formatInput(value)
        this.inputFormatted = value
        this.emitInput()
      }
    },
    emitInput() {
      const isValid = isValidPhoneNumber(this.inputFormatted)
      try {
        const foundPhoneNumber = parsePhoneNumberWithError(this.inputFormatted)
        const number = foundPhoneNumber.number
        this.$emit('input', { value: number, isValid })
      } catch (error) {
        this.$emit('input', { value: this.inputFormatted, isValid })
      }
    },
    preformatInputValue(value) {
      if (value.startsWith('8')) {
        return this.preformatInputValue(`+7${value.substring(1)}`)
      }
      if (value.startsWith('+8')) {
        return this.preformatInputValue(`+7${value.substring(2)}`)
      }
      if (!value.startsWith('+')) {
        return this.preformatInputValue(`+${value}`)
      }
      return value
    },
    checkIfCharPlusOrNumber(char) {
      const plusOrNumberRegex = /^[+0-9]$/
      return plusOrNumberRegex.test(char)
    },
    formatInput(value) {
      const asYouType = new AsYouType()
      return asYouType.input(value)
    },
  },
}
</script>

<style scoped></style>
