# kotix-carousel-vue

Simple usage developed for vue ts apps via gsap


## Parameters

- [x] Responsive
- [x] draggable
- [x] scroll
- [x] ease (https://gsap.com/docs/v3/Eases/)
- [x] ease duration
- [x] startFrom
- [x] autoPlay
- [x] autoPlayDuration

## Getting started

### Installation

First step is to install it using  `npm`:

```bash
npm i kotix-carousel-vue
```

### Usage

```vue
<script setup lang="ts">
   import { KotixSection, SectionItem } from 'kotix-carousel-vue';

</script>

<template>
   <KotixSection
            :scroll="true"      
            :controls="true"
            :autoPlay="true"
            :autoPlayDuration="1500"
   >
      <SectionItem>
         Your first section
      </SectionItem>
      <SectionItem>
         Your second section
      </SectionItem>
   </KotixSection>
</template>

<style>
   @import "kotix-carousel-vue";
   
</style>
```

### Props

| Name            | Type     | Required | Default       | Description                                                                          |
|-----------------|----------|----------|---------------|--------------------------------------------------------------------------------------|
| `draggable`     | Boolean  | No       | `false`       | Determines whether the carousel can be dragged horizontally.                         |
| `scroll`        | Boolean  | No       | `true`        | Enables automatic scrolling through the carousel items.                              |
| `ease`          | String   | No       | `'power2.inOut'` | The easing function used for animations for more info check out https://gsap.com/docs/v3/Eases/.                    |
| `duration`      | Number   | No       | `0.75`        | The duration of slide transitions in seconds.                                        |
| `startFrom`     | Number   | No       | `0`           | The index of the item to start from initially.                                       |
| `controls`      | Boolean  | No       | `false`       | Displays navigation controls (e.g., buttons) for the carousel.                       |
| `autoPlay`      | Boolean  | No       | `false`       | Automatically plays the carousel by scrolling through items at a specified interval. |
| `autoPlayDuration` | Number | No      | `3500`        | The duration in milliseconds between auto-play transitions.                          |


# KotixSection Component Defaults

This table lists the default values for each prop of the `KotixSection` component.

| Name            | Default Value |
|-----------------|---------------|
| `draggable`     | `false`       |
| `scroll`        | `true`        |
| `ease`          | `'power2.inOut'` |
| `duration`      | `0.75`        |
| `startFrom`     | `0`           |
| `controls`      | `false`       |
| `autoPlay`      | `false`       |
| `autoPlayDuration` | `3500`      |

# Css customization 

- **Customization**: Describes available CSS variables (`--ktx-progress-color`, `--ktx-btn`, `--carousel-control-bg`, `--ktx-progress-bg`) and how users can override them in their applications.
