<template>
  <div>
    <div class="a-carousel">
      <div class="a-carousel-item" v-for="n in slides" :key="n">
        <div>
          {{ n }}
        </div>
      </div>
    </div>
    <div>
      <button @click="prev">Prev</button>
      <button @click="next">Next</button>
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop } from "vue-property-decorator";

@Component
export default class Carousel extends Vue {
  @Prop({ default: 1 }) readonly perSlide!: number;

  private num: Array<any> = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
  private cloneNum: Array<any> = [...this.num];

  private isMobile: boolean = false;

  private startPoint: number = 0;
  private endPoint: number = this.responsivePerSlide;

  public prev(): void {
    this.startPoint -= 1;
    this.endPoint -= 1;

    if (this.startPoint < 0) {
      const lastElement = this.cloneNum.pop();

      if (lastElement !== undefined) {
        this.cloneNum.unshift(lastElement);
      }

      this.startPoint = 0;
      this.endPoint = this.isMobile ? 0 : this.responsivePerSlide;
    }
  }

  public next(): void {
    this.startPoint += 1;
    this.endPoint += 1;
    if (this.endPoint > this.num.length - this.responsivePerSlide) {
      const firstElement = this.cloneNum.shift();
      if (firstElement !== undefined) {
        this.cloneNum.push(firstElement);
      }
      this.endPoint = this.num.length - 1;
      this.startPoint = this.isMobile
        ? this.num.length - 1
        : this.num.length - this.responsivePerSlide - 1;
    }
  }

  get slides(): Array<number> {
    return this.cloneNum.slice(this.startPoint, this.endPoint + 1);
  }

  get responsivePerSlide(): number {
    return this.isMobile ? 1 : this.perSlide;
  }

   public handleResize(): void {
    this.isMobile = window.innerWidth <= 767;
    this.endPoint = this.isMobile ? this.startPoint : this.responsivePerSlide;
  }

  mounted() {
    window.addEventListener('resize', this.handleResize);
    this.handleResize();
  }

  destroyed() {
    window.removeEventListener('resize', this.handleResize);
  }
}
</script>

<style lang="scss">
.a-carousel {
  width: 100%;
  display: inline-grid;
  grid-template-columns: auto auto auto;
  column-gap: 1rem;
  row-gap: 1rem;
  color: white;
  font-size: 32px;

  @media only screen and (max-width: 767px) {
    grid-template-columns: auto;
  }

  .a-carousel-item {
    display: flex;
    justify-content: center;
    align-items: center;
    background: lightblue;
    height: 200px;
  }
}
</style>
