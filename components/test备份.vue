<template>
  <div>
    <div class="container">
      <div class="inner">
        <img :src="`https://picsum.photos/id/5${i + 1}/600`" alt="" srcset="" v-for="i in 6" />
        <img :src="`https://picsum.photos/id/5${i + 1}/600`" alt="" srcset="" v-for="i in 6" />
        <img :src="`https://picsum.photos/id/5${i + 1}/600`" alt="" srcset="" v-for="i in 6" />
        <img :src="`https://picsum.photos/id/5${i + 1}/600`" alt="" srcset="" v-for="i in 6" />
      </div>
    </div>
  </div>
</template>
<script setup lang="ts"></script>
<style lang="scss" scoped>
  @use 'sass:math';

  $size: 400px;
  $n: 24;
  $pDeg: calc(360deg / $n);
  $r: calc($size / 2);
  $R: calc($r / math.sin(calc($pDeg / 2))); // 这是算出等腰三角形的半径公式
  $innerSize: calc($R * 2);

  .container {
    width: $size;
    height: $size;
    // outline: 5px solid white;
    // border-radius: 50%;
    margin: 50px auto;
    display: flex;
    justify-content: center;
    // overflow: hidden;
    margin-top: 50px;
  }

  .inner {
    width: $innerSize;
    height: $innerSize;
    border-radius: 50%;
    flex-shrink: 0;
    margin-top: $r;
    position: relative;
    img {
      width: calc($size - 100px);
      height: calc($size - 100px);
      //border-radius: 50%;
      border-radius: 8px;
      position: absolute;
      left: 50%;
      margin-left: math.div(-$size, 2);
      top: math.div(-$size, 2);
      transform-origin: center #{$r + $R};
      @for $i from 1 through $n {
        &:nth-child(#{$i}) {
          transform: rotate($pDeg * ($i - 1));
        }
      }
    }
  }

  $u: calc(1 / $n * 100%);
  $stopPercent: 0.6 * $u;
  @keyframes moving {
    @for $i from 1 through $n {
      $p: calc($u * $i);
      $deg: calc($pDeg * $i);
      #{$p - $stopPercent},
      #{$p} {
        transform: rotate(-$deg);
      }
    }
  }
  .inner {
    // animation: moving 50s infinite;
  }
</style>
