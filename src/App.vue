<template>
  <Header />

  <div class="inputs">
    <input type="color" v-model="colorInput" class="inputs-color" />
    <input type="text" v-model="colorInput" class="inputs-text" />
    <button @click="addColor(colorInput)" class="button">Add color</button>
  </div>

  <div class="palette">
    <h2>Palette</h2>
    <ul class="colors">
      <li 
        v-for="(color, index) in colorArray" 
        :key="index" 
        class="color"
        :style="`background: ${colorArray[index].hex}`"
      >
        <div class="color-info">
          {{ colorArray[index].hex }}<br />
          rgb({{colorArray[index].rgb.r}}, {{colorArray[index].rgb.g}}, {{colorArray[index].rgb.b}})
        </div>
      </li>
    </ul>
  </div>

  <div class="combinations">
    <h2>Combinations & contrast</h2>
    <ul class="combination-list">
      <li 
        v-for="(color, index) in colorArray" 
        :key="index" 
        class="combination-color"
      >
        <div class="combination-color-name" :style="`color: ${color.hex}`">{{ color.hex }}</div>
        <ul class="combination-wrapper">
          <li 
            v-for="(subcolor, index) in filteredCombinations(colorArray, color)" 
            :key="index" 
            class="combination-subcolor"
            :style="`background: ${color.hex}`"
          >
            {{ contrastRatio(color, subcolor) }}
            <div :style="`color: ${subcolor.hex}`">{{ subcolor.hex }}</div>

            <ul class="contrast-levels">
              <li class="contrast-level largetext" :class="`${contrastRatio(color, subcolor) < 1/3 ? 'pass' : 'fail'}`">AA</li>
              <li class="contrast-level smalltext" :class="`${contrastRatio(color, subcolor) < 1/4.5 ? 'pass' : 'fail'}`">AA</li>
              <li class="contrast-level largetext" :class="`${contrastRatio(color, subcolor) < 1/4.5 ? 'pass' : 'fail'}`">AAA</li>
              <li class="contrast-level smalltext" :class="`${contrastRatio(color, subcolor) < 1/7 ? 'pass' : 'fail'}`">AAA</li>
            </ul>
            <!--AA-level large text: {{contrastRatio(color, subcolor) < 1/3 ? 'PASS' : 'FAIL' }}<br>
                AA-level small text: {{contrastRatio(color, subcolor) < 1/4.5 ? 'PASS' : 'FAIL' }}<br>
                AAA-level large text: {{contrastRatio(color, subcolor) < 1/4.5 ? 'PASS' : 'FAIL' }}<br>
                AAA-level small text: {{contrastRatio(color, subcolor) < 1/7 ? 'PASS' : 'FAIL' }}-->
          </li>
        </ul>
      </li>
    </ul>
  </div>

  {{colorArray}}

  <Footer />
</template>

<script>
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'

export default {
  name: 'Colors',
  components: {
    Header,
    Footer
  },
  data() {
    return {
      colorInput: '#ffffff',
      colorArray: []
    }
  },
  methods: {
    addColor(color) {
      const colorItem = {
        hex: color,
        rgb: this.hexToRgb(color)
      }
      this.colorArray.push(colorItem)
    },
    filteredCombinations(array, color) {
      return array.filter(item => {
        return item.hex !== color.hex
      })
    },
    hexToRgb(hex) {
      // function by Tim Down
      var shorthandRegex = /^#?([a-f\d])([a-f\d])([a-f\d])$/i;
      hex = hex.replace(shorthandRegex, function(m, r, g, b) {
        return r + r + g + g + b + b;
      });

      var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
      return result ? {
        r: parseInt(result[1], 16),
        g: parseInt(result[2], 16),
        b: parseInt(result[3], 16)
      } : null;
    },
    luminance(r, g, b) {
      // function by kirilloid
      var a = [r, g, b].map(function (v) {
          v /= 255;
          return v <= 0.03928
              ? v / 12.92
              : Math.pow( (v + 0.055) / 1.055, 2.4 );
      });
      return a[0] * 0.2126 + a[1] * 0.7152 + a[2] * 0.0722;
    },
    contrastRatio(color1, color2) {
      const color1luminance = this.luminance(color1.rgb.r, color1.rgb.g, color1.rgb.b);
      const color2luminance = this.luminance(color2.rgb.r, color2.rgb.g, color2.rgb.b);

      const ratio = color1luminance > color2luminance 
      ? ((color2luminance + 0.05) / (color1luminance + 0.05))
      : ((color1luminance + 0.05) / (color2luminance + 0.05));

      return ratio
    }
  }
}
</script>

<style lang="scss" scoped>
.inputs {
  padding: 1rem;
  display: flex;
  gap: .25rem;

  &-color, &-text, .button {
    font-family: inherit;
    font-size: inherit;
    font-weight: inherit;
    color: inherit;
    border: 1px solid var(--color-text);
  }

  &-color {
    height: 2.5rem;
    width: 2.5rem;
  }

  &-text {
    padding: .5rem;
  }
}

.button {
  padding: .5rem 1rem;
}

.palette {
  padding: 1rem;
}
.colors {
  list-style: none;
  margin: 0;
  padding: 0;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 1rem;
}
.color {
  background: #ddd;
  padding-top: 100%;
  position: relative;

  &-info {
    position: absolute;
    bottom: 0;
    left: 0;
    padding: .5rem;
  }
}

.combinations {
  padding: 1rem;
}
.combination {
  &-list {
    list-style: none;
    margin: 0;
    padding: 0;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-gap: 1rem;
  }
  &-color {
    position: relative;

    &-name {
      padding: .5rem;
      margin-bottom: .5rem;
    }
  }
  &-wrapper {
    list-style: none;
    margin: 0;
    padding: 0;
    display: grid;
    grid-template-columns: 1fr;
    gap: .5rem;
  }
  &-subcolor {
    display: block;
    padding: .5rem;
  }
}

.contrast-levels {
  list-style: none;
  margin: 0;
  padding: 0;
}
.contrast-level {
  display: inline-block;
  vertical-align: middle;
  background: #000;
  color: #fff;
  padding: .2rem;
  margin-right: .2rem;
  font-size: .6rem;
  line-height: 1rem;

  &.smalltext {
    font-size: .6rem;
  }
  &.largetext {
    font-size: 1rem;
  }
  &.fail {
    color: red;
    &:after {
      content: " x";
      font-size: 1rem;
    }
  }
  &.pass {
    color: lime;
    &:after {
      content: " ☑";
      font-size: 1rem;
    }
  }
}
</style>
