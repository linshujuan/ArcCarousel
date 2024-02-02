<template>
  <div>
    <div
      class="relative carousel-container"
      @mousedown.prevent="startDrag"
      @touchstart.prevent="startDrag"
      @mousemove.prevent="handleDragMove"
      @touchmove.prevent="handleDragMove"
      @mouseup.prevent="endDrag"
      @touchend.prevent="endDrag"
    >
      <div class="inner" :style="{ transform: `rotate(${-rotation}deg) ` }" :class="{ animation: animation }">
        <div v-for="(item, index) in allPictures" :key="item" class="item" :class="{ 'mt-[-14px]': currentSlide === index }">
          <img :src="item" alt="" srcset="" :title="index + 1" v-if="displayPosition(index)" :class="{ 'scale-95': currentSlide !== index }" />
        </div>
      </div>
      <div v-show="pictures?.length > 0" class="flex items-center justify-center my-[20px] mb-10 w-full z-[100] absolute top-[600px]">
        <!-- position:{{ position }} //rotation:{{ rotation }} // currentSlide:{{ currentSlide }} -->
        <span path="arrow_left_straight" type="svg" :w="18" :h="16" class="cursor-pointer text-blue mr-[26px]" @click="prev">prev</span>
        <div class="flex items-center">
          <div
            v-for="(item, index) in pictures?.length"
            :key="index"
            class="rounded-full cursor-pointer w-[12px] h-[12px] bg-zinc-500 mr-[10px]"
            :class="{ '!bg-pink w-[16px] h-[16px]': currentSlide % 5 === index }"
            @click="clickHandle(index)"
          ></div>
        </div>
        <span path="arrow_left_straight" type="svg" :w="18" :h="16" class="cursor-pointer text-blue ml-[26px]" @click="next">next</span>
        <!-- <ms-icon path="arrow_left_straight" type="svg" :w="18" :h="16" class="transform rotate-180 cursor-pointer text-blue ml-[16px]" @click="next" /> -->
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
  const pictures = ref(['https://picsum.photos/id/50/600', 'https://picsum.photos/id/51/600', 'https://picsum.photos/id/52/600', 'https://picsum.photos/id/53/600', 'https://picsum.photos/id/54/600']);

  const currentSlide = ref(0);
  const rotation = ref(0);
  const position = ref(0);
  const dragStartX = ref(0);
  const isDragging = ref(false);
  const total = ref(pictures.value?.length);
  const animation = ref(true);
  const pDeg = ref(4); // The rotate degree per picture
  const allTotal = ref(360 / pDeg.value);

  const allPictures = computed(() => {
    const all = [] as Array<string>;
    for (let index = 0; index < allTotal.value / total.value; index++) {
      pictures.value.forEach((element) => {
        all.push(element);
      });
    }
    return all;
  });

  const displayPosition = (index: number) => {
    // return index < 6 || index > 88;
    // return index < 7 || index > 86;
    return true;
    return index < 7 || index > 83;
  };

  const prev = () => {
    if (currentSlide.value === 0) {
      currentSlide.value = allTotal.value - 1;
    } else {
      currentSlide.value--;
    }
    rotation.value -= pDeg.value;
  };

  const next = () => {
    if (currentSlide.value === allTotal.value - 1) {
      currentSlide.value = 0;
    } else {
      currentSlide.value++;
    }
    rotation.value += pDeg.value;
  };

  const clickHandle = (index: number) => {
    const steps = index - (currentSlide.value % 5);
    currentSlide.value += steps;
    rotation.value += pDeg.value * steps;
  };

  let rotationClone: number;
  const startDrag = (event) => {
    isDragging.value = true;
    dragStartX.value = getEventX(event);
    rotationClone = Number(JSON.parse(JSON.stringify(rotation.value)));
  };

  const tempRotate = ref(0);
  const handleDragMove = (event) => {
    if (isDragging.value) {
      rotation.value = rotationClone;
      const dragDelta = getEventX(event) - dragStartX.value;
      position.value = -dragDelta;
      tempRotate.value = position.value / 122; // $size/pDeg
      rotation.value = rotation.value + tempRotate.value;
    }
  };

  const endDrag = () => {
    rotation.value = rotationClone;
    if (isDragging.value) {
      isDragging.value = false;
      const tolerance = 50;
      if (position.value >= tolerance) {
        next();
      }
      if (position.value <= -tolerance) {
        prev();
      }
      position.value = 0;
    }
  };

  const getEventX = (event) => {
    return event.clientX || (event.touches && event.touches[0].clientX);
  };
</script>

<style lang="scss" scoped>
  @use 'sass:math';

  $size: 488px; // The slider card height
  $n: 90; // The allTotal value
  $pDeg: calc(360deg / $n);
  $r: calc($size / 2);
  $R: calc($r / math.sin(calc($pDeg / 2))); // This is the formula for calculating the radius of an isosceles triangle
  $innerSize: calc($R * 2);

  .carousel-container {
    // width: $size;
    height: calc($size + 680px);
    margin: 0 auto;
    display: flex;
    justify-content: center;
    overflow: hidden;
    margin-top: 50px;
    padding-top: 40px;
  }

  .inner {
    width: $innerSize;
    height: $innerSize;
    border-radius: 50%;
    flex-shrink: 0;
    margin-top: $r;
    position: relative;
    .item {
      width: calc($size - 80px);
      height: $size;
      position: absolute;
      left: 50%;
      margin-left: calc(math.div(-$size, 2) + 40px);
      top: math.div(-$size, 2);
      transform-origin: center #{$r + $R};
      overflow: hidden;
      @for $i from 1 through $n {
        &:nth-child(#{$i}) {
          transform: rotate($pDeg * ($i - 1));
        }
      }
      img {
        width: 100%;
        height: 100%;
        border-radius: 24px;
        //box-shadow: 0 0 5px 7px rgba(255, 255, 255, 0.5);
      }
    }
  }

  $u: calc(1 / $n * 100%);
  $stopPercent: 0.6 * $u;
  // @keyframes moving {
  //  @for $i from 1 through $n {
  // $p: calc($u * $i);
  // $deg: calc($pDeg * $i);
  // #{$p - $stopPercent},
  //  #{$p} {
  //   transform: rotate(-$deg);
  //    }
  //  }
  // }

  .inner {
    &.animation {
      transition: transform 0.4s linear;
    }
  }
</style>
