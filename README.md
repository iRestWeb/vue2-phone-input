# vue2-phone-input
Phone input with country list, country codes ..

## Installation
---------------
### npm
``` sh
npm install --save vue2-phone-input
npm install --save less-loader less
```

## Usage
---------------

```vue
<script>
import vue from 'vue';
import PhoneInput from 'vue2-phone-input';

vue.component('phone-input', PhoneInput);

export default {
  name: 'hello',
  data() {
    return {
      phone: {
        code: '',
        number: ''
      }
    }
  },
};
</script>

<template>
  <div class="hello">    
    <phone-input :phone="phone"/>
  </div>
</template>
```

## Thanks
---------------
Thanks to @sulieman-sh
