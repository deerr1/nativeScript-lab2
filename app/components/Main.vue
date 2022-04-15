<template>
  <Page>
    <ActionBar>
      <Label text="Конвертер" />
    </ActionBar>
    <ScrollView >
      <FlexboxLayout
        flexDirection="row"
        flexWrap="wrap"
        alignContent="space-between"
        class="container"
      >
        <StackLayout
          v-for="metric in metrics"
          :key="metric"
          class="box"
          @tap="$navigateTo(metric.component, {props: {name: metric.name}})"
        >
          <Image
            class="img"
            :src="'~/images/'+metric.name+'.png'"
            stretch="aspectFit"
          />
          <Label :text="metric.name" textAlignment="center" />
        </StackLayout>
      </FlexboxLayout>
    </ScrollView>
  </Page>
</template>

<script>
import Binary from "@/components/detail_actions/Binary.vue"
import LengthDistance from "@/components/detail_actions/LengthDistance.vue"
import Square from "@/components/detail_actions/Square.vue"
import Temperature from "@/components/detail_actions/Temperature.vue"
import Time from "@/components/detail_actions/Time.vue"
import Weight from "@/components/detail_actions/Weight.vue"

export default {
  data: function () {
    return {
      metrics: [
        {name: "Площадь", component: Square},
        {name: "Системы счисления", component: Binary},
        {name: "Длина и расстояние", component: LengthDistance},
        {name: "Время", component: Time},
        {name: "Вес", component: Weight},
        {name: "Температура", component: Temperature},
      ],
    };
  },
  methods: {

  }
};
</script>

<style scoped lang="scss">
.container {
  max-height: 100%;
  background-color: rgb(206, 206, 206);
}
.box {
  width: 33%;
  background-color: white;
  margin: 6px, 2px, 0, 2px;
}
</style>
