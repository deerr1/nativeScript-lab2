<template>
  <GridLayout columns="*, *, *, *" rows="auto, auto, auto, auto, auto, auto">
    <FlexboxLayout v-if="btnHex" row=0 col=0 columnSpan="4">
        <Button text="F" @tap="addNumber('F')" class="button"/>
        <Button text="E" @tap="addNumber('E')" class="button"/>
        <Button text="D" @tap="addNumber('D')" class="button"/>
        <Button text="C" @tap="addNumber('C')" class="button"/>
    </FlexboxLayout>

    <Button text="7" row="1" col="0" @tap="addNumber('7')" class="button" />
    <Button text="8" row="1" col="1" @tap="addNumber('8')" class="button" />
    <Button text="9" row="1" col="2" @tap="addNumber('9')" class="button" />

    <Button text="4" row="2" col="0" @tap="addNumber('4')" class="button" />
    <Button text="5" row="2" col="1" @tap="addNumber('5')" class="button" />
    <Button text="6" row="2" col="2" @tap="addNumber('6')" class="button" />

    <Button text="1" row="3" col="0" @tap="addNumber('1')" class="button" />
    <Button text="2" row="3" col="1" @tap="addNumber('2')" class="button" />
    <Button text="3" row="3" col="2" @tap="addNumber('3')" class="button" />

    <Button text="0" row="4" col="1" @tap="addNumber('0')" class="button" />
    <Button text="." row="4" col="2" @tap="addDot" class="button" />

    <FlexboxLayout flexDirection="column" row="1" col="3" rowSpan="4">
      <Button v-if="btnHex" text="B" @tap="addNumber('B')" class="button" />
      <Button v-if="btnHex" text="A" @tap="addNumber('A')" class="button" />
      <Button text="AC" @tap="clearAll" class="button" />
      <Button text="<-" @tap="clearNumber" class="button" />
      <Button v-if="btnSign" text="+/-" @tap="changeSign" class="button" />
    </FlexboxLayout>
  </GridLayout>
</template>

<script>
export default {
  props: { field: String, plusMinus: Boolean, hex: Boolean},
  emits: ["fieldChanged"],
  data: function() {
      return {
          btnSign: this.plusMinus || false,
          btnHex: this.hex || false,
      }
  },
  methods: {
    addNumber: function (num) {
      if (this.removeMinus(this.field) === "0") {
        this.field = (this.field.indexOf("-") != -1 ? "-"  : "") + num ;
      } else {
        this.field += num;
      }
      this.$emit("fieldChanged", this.field);
    },
    clearNumber: function () {
      if (this.removeMinus(this.field).length != 1) {
        this.field = this.field.slice(0, -1);
        this.$emit("fieldChanged", this.field);
      } else {
        this.clearAll();
      }
    },
     clearAll: function () {
      this.field = "0";
      this.$emit("fieldChanged", this.field);
    },
    addDot: function () {
      if (this.field.indexOf(",") == -1) {
        this.field += ".";
        this.$emit("fieldChanged", this.field);
      }
    },
    changeSign: function () {
      if (this.field.indexOf("-") != -1) {
        this.field = this.field.slice(1);
      } else {
        this.field = "-" + this.field;
      }
      this.$emit("fieldChanged", this.field);
    },
    removeMinus: function(num) {
        if (this.field.indexOf("-") != -1){
            return num.slice(1);
        }
        else {
            return num;
        }
    }
  },
};
</script>

<style>
.button {
  font-size: 25px;
}
</style>
