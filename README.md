# Dragon Drop

## Installation

```sh
npm i @pderas/dragon-drop
```

## Setup
```js
import DragonDrop from '@pderas/dragon-drop';
Vue.use(DragonDrop);
```

## Usage
```vue
<template>
<DragonDrop v-model="numbers" v-slot="{ item: number }">
    {{ number }}
</DragonDrop>
</template>

<script>
export default {
    data: _ => ({
        numbers: [1,2,3,4,5,6,7,8,9,10]
    })
}
</script>

<style>
/**
 * No styles are supplied, however the vue
 * transition group name defaults to
 * 'dragon-drop' for easy use.
 */
.dragon-drop-move {
  transition: transform 0.5s;
}
</style>
```


## Props
| Prop        | type           | required/default  | Description |
| --- | --- | --- | --- |
| value | Array | required | the array of items to be rendered. This is usually not needed and `v-model` is to be used instead |
| itemKey | String | 'id' | the unique field on an item an item as the key, if this fails to get a key, the whole item will be used instead |
| tag | String | 'span' | the base tag of the wrapper 'DragonDrop' element |
| transition | String | 'dragon-drop' | The vue transition name to be applied |

## Events

| Event | Parameters | Description |
| --- | --- | --- |
| input | Array | returns the updated (sorted) array |
| drag-start | Object | returns the item that has been picked up |
| drag-end | Object | returns the item that has been replaced |
