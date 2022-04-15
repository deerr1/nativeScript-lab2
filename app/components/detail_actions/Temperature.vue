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
        :plusMinus="true"
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
      fields: ["1", "33.8"],
      typesOfFields: [
        { name: "Цельсий", shortName: "C", coef: "10" },
        { name: "Фаренгейт", shortName: "F", coef: "10" },
      ],
      types: [
        { name: "Цельсий", shortName: "C", coef: "10" },
        { name: "Фаренгейт", shortName: "F", coef: "10" },
        { name: "Кельвин", shortName: "K", coef: "10" },
      ],
    };
  },
  methods: {
    changeValue(num) {
      this.fields.splice(Number(this.activeField), 1, num);
      this.convert();
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
      switch (
        this.typesOfFields[Number(this.activeField)].shortName + this.typesOfFields[Number(!this.activeField)].shortName
      ) {
        case "CF": {
          value = this.CToF(value);
          break;
        }
        case "FC": {
          value = this.FToC(value);
          break;
        }
        case "CK": {
          value = this.CToK(value);
          break;
        }
        case "KC": {
          value = this.KToC(value);
          break;
        }
        case "KF": {
          value = this.KToF(value);
          break;
        }
        case "FK": {
          value = this.FToK(value);
          break;
        }
      }
      this.fields.splice(Number(!this.activeField), 1, String(value));
    },
    CToK(num) {
      return num + 273.15;
    },
    KToC(num) {
      return num - 273.15;
    },
    CToF(num) {
      return num * 1.8 + 32;
    },
    FToC(num) {
      return (num - 32) / 1.8;
    },
    FToK(num) {
      return this.CToK(this.FToC(num));
    },
    KToF(num) {
      return this.CToF(this.KToC(num));
    },
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
