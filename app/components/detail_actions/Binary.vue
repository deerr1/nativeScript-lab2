<template>
  <Page>
    <ActionBar>
      <NavigationButton
        @tap="$navigateBack()"
        android.systemIcon="ic_menu_back"
      />
      <Label :text="name" />
    </ActionBar>
    <FlexboxLayout
      flexDirection="column"
      justifyContent="space-between"
      class="box"
    >
      <FlexboxLayout justifyContent="space-between">
        <FlexboxLayout
          flexDirection="column"
          justifyContent="space-between"
          class="column"
        >
          <Label
            :text="typesOfFields[0].shortName"
            @tap="changeType(0)"
            class="label"
            textDecoration="underline"
          />
          <Label
            :text="typesOfFields[1].shortName"
            @tap="changeType(1)"
            class="label"
            textDecoration="underline"
          />
        </FlexboxLayout>
        <FlexboxLayout
          flexDirection="column"
          justifyContent="space-between"
          class="column"
        >
          <StackLayout>
            <Label
              :text="fields[0]"
              textWrap="true"
              @tap="activeField ? (activeField = !activeField) : null"
              textAlignment="right"
              textTransform="uppercase"
              :class="[!activeField ? 'active' : null, 'label']"
            />
            <Label
              :text="typesOfFields[0].name"
              textAlignment="right"
              class="helpText"
            />
          </StackLayout>
          <StackLayout>
            <Label
              :text="fields[1]"
              textWrap="true"
              @tap="!activeField ? (activeField = !activeField) : null"
              textAlignment="right"
              textTransform="uppercase"
              :class="[activeField ? 'active' : null, 'label']"
            />
            <Label
              :text="typesOfFields[1].name"
              textAlignment="right"
              class="helpText"
            />
          </StackLayout>
        </FlexboxLayout>
      </FlexboxLayout>
      <Keyboard
        :field="fields[Number(activeField)]"
        :hex="true"
        @fieldChanged="changeValue"
      />
    </FlexboxLayout>
  </Page>
</template>

<script>
import Keyboard from "~/components/detail_actions/Keyboard.vue";
export default {
  components: {
    Keyboard,
  },
  props: ["name"],
  data: function () {
    return {
      activeField: true,
      fields: ["1010", "10"],
      typesOfFields: [
        { name: "Двоичная", shortName: "BIN", coef: "2" },
        { name: "Десятичная", shortName: "DEC", coef: "10" },
      ],
      types: [
        { name: "Двоичная", shortName: "BIN", coef: "2" },
        { name: "Восьмиричная", shortName: "OCT", coef: "8" },
        { name: "Десятичная", shortName: "DEC", coef: "10" },
        { name: "Шестнадцатеричная", shortName: "HEX", coef: "16" },
      ],
    };
  },
  methods: {
    changeValue(num) {
      this.fields.splice(Number(this.activeField), 1, num);
      this.convert();
      if (isNaN(this.fields[0]) || isNaN(this.fields[1])) {
        this.fields.splice(0, 1, "0");
        this.fields.splice(1, 1, "0");
      }
    },
    changeType(index) {
      action(
        "Выберите тип измерения",
        "Отменить",
        this.types.map((x) => x.name)
      ).then((result) => {
        if (result !== "Отменить") {
          this.typesOfFields.splice(
            index,
            1,
            this.types.find((x) => x.name == result)
          );
          this.convert();
        }
      });
    },
    convert() {
      let value = this.fields[Number(this.activeField)];
      let decValue = this.toDec(value, this.typesOfFields[Number(this.activeField)].coef)
      value = this.fromDec(decValue, this.typesOfFields[Number(!this.activeField)].coef)
      this.fields.splice(Number(!this.activeField), 1, String(value));

      value = this.fields[Number(!this.activeField)];
      decValue = this.toDec(value, this.typesOfFields[Number(!this.activeField)].coef)
      value = this.fromDec(decValue, this.typesOfFields[Number(this.activeField)].coef)
      this.fields.splice(Number(this.activeField), 1, String(value));
    },
    toDec(num, system) {
      return parseInt(num, system)
    },
    fromDec(num, system) {
      return Number(num).toString(system);
    }
  },
};
</script>

<style>
.active {
  color: orange;
}
.box {
  margin: 5%, 2%, 5%, 2%;
}
.column {
  height: 24%;
}
.label {
  width: 95%;
  font-size: 22px;
}
.helpText {
  font-size: 13px;
}
</style>
