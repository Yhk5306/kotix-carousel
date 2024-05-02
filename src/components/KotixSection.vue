<template>
  <div class="kotix-carousel">
    <div class="carousel-arranger" ref="sectionItem">
        <slot></slot>
    </div>
    <div class="carousel-controls" v-if="controls">
      <ul class="carousel-button-list" :style="{ width: `calc(${(items * 20)}px + 25px)` }">
        <li class="carousel-btn " :class="{'active-carousel-btn': index === currentSlide +1}"  v-for="index in items" @click="goTo(index - 1)" >
          <div class="playback-bar">
            <div class="start" > </div>
            <div class="spoti-progress-bar">
              <div class="progress-bar-line"   :style="{
  '--progress-bar-transform': index === currentSlide +1 ? progress + '%' : '0%'
}">
                <div class="progress-bar-background" >
                  <div  class="progress-arranger">
                    <div class="progress-bg"></div>
                  </div>
                  <div class="progress-button"></div>
                </div>
              </div>
            </div>
          </div>
        </li>
      </ul>
      <div class="carousel-play-btn" v-if="autoPlay">
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24" @click="play()" v-if="!isCarouselActive" ><path fill="currentColor" d="M8 19V5l11 7l-11 7Z" /></svg>
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24" @click="pauseCarousel()" v-if="isCarouselActive" ><path fill="currentColor" d="M14 19V5h4v14h-4Zm-8 0V5h4v14H6Z" /></svg>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import {defineComponent, onMounted, ref} from 'vue'
import {gsap} from "gsap";

import ScrollTrigger from "gsap/ScrollTrigger";
import Observer from "gsap/Observer";
gsap.registerPlugin(ScrollTrigger);
gsap.registerPlugin(Observer)

export default defineComponent({
  name: "KotixSection",
  props:{
    draggable:{
      type:Boolean,
      required:false,
      default:false,
    },
    scroll:{
      type:Boolean,
      required:false,
      default:true,
    },
    ease:{
      type:String,
      required:false,
      default:'power2.inOut'
    },
    duration:{
      type:Number,
      required:false,
      default:.75
    },
    startFrom:{
      type:Number,
      required:false,
      default:0
    },
    controls:{
      type:Boolean,
      required:false,
      default:false,
    },
    autoPlay:{
      type:Boolean,
      required:false,
      default:false
    },
    autoPlayDuration:{
      type:Number,
      required:false,
      default:3500
    }
  },
  setup(props){
    const isCarouselActive = ref(false)
    const itemRefs = ref([])
    const sectionItem = ref(null);
    const items = ref(0)

    onMounted(() => {
      itemRefs.value = sectionItem.value.children;
      items.value = sectionItem.value.children.length;
      goTo(props.startFrom)
      observer();
    });


    let currentIndex = -1;
    let animating;

    const translateAmount = 30, rotateAmount = 10;



    const goTo = (index, direction = 'down') => {
      const sections = itemRefs.value;

      const wrap = gsap.utils.wrap(0, sections.length);

      index = wrap(index);
      animating = true;
      let leave, enter;
      const defaultPos = { transform: 'none', autoAlpha: 1 };


      const tl = gsap.timeline({
        defaults: { duration: props.duration, ease:props.ease, autoAlpha: 0 },
        onComplete: () => {
          animating = false;
        }
      });



      switch(direction) {
        case 'up':
          leave = { yPercent: translateAmount, rotationX: -rotateAmount };
          enter = { yPercent: -translateAmount, rotationX: rotateAmount };
          break;
        case 'down':
          leave = { yPercent: -translateAmount, rotationX: rotateAmount };
          enter = { yPercent: translateAmount, rotationX: -rotateAmount };
          break;
        case 'left':
          leave = { xPercent: -translateAmount, rotationY: -rotateAmount };
          enter = { xPercent: translateAmount, rotationY: rotateAmount };
          break;
        case 'right':
          leave = { xPercent: translateAmount, rotationY: rotateAmount };
          enter = { xPercent: -translateAmount, rotationY: -rotateAmount};
          break;
      }



      tl.fromTo(sections[index], enter, defaultPos);

      currentSlide.value = index;

      startTime = Date.now();
      progress.value = 0;
      currentTime.value = 0;

      if(currentIndex > -1 || currentIndex === 0) {
        tl
            .to(sections[currentIndex], leave, 0)
            .set(sections[currentIndex], { transform: 'none' });
      }



      currentIndex = index;
    }

    let observerInstance;

    let observer;


    if (props.scroll && !props.draggable){
      observer = () => {
        observerInstance = Observer.create({
          wheelSpeed: -1,
          tolerance: 0,
          onDown: () => !animating && goTo(currentIndex - 1, 'up'),
          onUp: () => !animating && goTo(currentIndex + 1, 'down'),
          onLeft: () => !animating && goTo(currentIndex + 1, 'left'),
          onRight: () => !animating && goTo(currentIndex - 1, 'right'),
          preventDefault:ScrollTrigger.isTouch,         //added for mobile controls
          onPress: self => {
            ScrollTrigger.isTouch && self.event.preventDefault();
          }
        });
      };
    }

    if (props.draggable){
      observer = () => {
        observerInstance = Observer.create({
          wheelSpeed: -1,
          tolerance: 20,
          onLeft: () => !animating && goTo(currentIndex + 1, 'left'),
          onRight: () => !animating && goTo(currentIndex - 1, 'right'),
          preventDefault: true,
          onPress: self => {
            // Disable dragging by preventing default touch behavior
            if (ScrollTrigger.isTouch) {
              self.event.preventDefault();
            }
          },
          onDragStart: () => {
            // Disable dragging by preventing the drag start event
            return false;
          }
        })
      }
    }


    let startTime = Date.now();
    let timerId;
    const progress = ref(0);
    const currentTime = ref(0)
    const currentSlide = ref(0)


    const countTime = (() => {
      if (props.autoPlay){
        const result = Date.now() - startTime;
        currentTime.value = result;
        progress.value = parseInt(String((result / props.autoPlayDuration) * 100));

        if (result >= props.autoPlayDuration) {
          currentSlide.value += 1
          goTo(currentSlide.value)
          startTime = Date.now();
          progress.value = 0;
          clearTimeout(timerId);
          countTime();
        } else {
          timerId = setTimeout(countTime, 100);
        }
      }else {
        return
      }

    })


    const play = (() => {
      isCarouselActive.value= true;
      countTime()
    })

    const pauseCarousel = (() => {
      isCarouselActive.value = false;
      startTime = Date.now() - currentTime.value;
      clearTimeout(timerId)
    })


    return {
      itemRefs,
      play,
      currentSlide,
      progress,
      isCarouselActive,
      goTo,
      pauseCarousel,
      sectionItem,
      items
    }

  }
})
</script>

<style  lang="scss">

</style>