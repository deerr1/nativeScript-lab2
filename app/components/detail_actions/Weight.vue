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
      fields: ["1000", "1"],
      typesOfFields: [
        { name: "Грамм", shortName: "г", coef: "1000" },
        { name: "Килограмм", shortName: "кг", coef: "1000" },
      ],
      types: [
        { name: "Миллиграмм", shortName: "мг", coef: "1000" },
        { name: "Грамм", shortName: "г", coef: "1000" },
        { name: "Килограмм", shortName: "кг", coef: "1000" },
        { name: "Тонна", shortName: "т", coef: "1000" },
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
      let firstIndex = this.types.findIndex(
        (x) => x.name == this.typesOfFields[0].name
      );
      let secondIndex = this.types.findIndex(
        (x) => x.name == this.typesOfFields[1].name
      );
      let result = this.fields[Number(this.activeField)];
      if (this.activeField) {
        if (firstIndex < secondIndex) {
          for (let i = firstIndex + 1; i <= secondIndex; i++) {
            result *= this.types[i].coef;
          }
        } else {
          for (let i = firstIndex; i > secondIndex; i--) {
            result /= this.types[i].coef;
          }
        }
      } else {
        if (firstIndex < secondIndex) {
          for (let i = firstIndex + 1; i <= secondIndex; i++) {
            result /= this.types[i].coef;
          }
        } else {
          for (let i = firstIndex; i > secondIndex; i--) {
            result *= this.types[i].coef;
          }
        }
      }
      this.fields.splice(Number(!this.activeField), 1, String(result));
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
