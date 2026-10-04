<template>
  <!-- При клике на весь тогл вызываем метод toggle -->
  <div 
    class="toggle-container" 
    :class="{ 'toggle-active': modelValue }" 
    @click="toggle"
  >
    <!-- Кругляшок, который будет двигаться -->
    <div class="toggle-handle"></div>
  </div>
</template>

<script>
export default {
  name: 'my-toggle',
  props: {
    // Во Vue 3 стандартное имя пропса для v-model — это modelValue
    modelValue: {
      type: Boolean,
      default: false
    }
  },
  emits: ['update:modelValue'], // Объявляем событие изменения
  methods: {
    toggle() {
      // Отправляем родителю противоположное значение булевой переменной
      this.$emit('update:modelValue', !this.modelValue);
    }
  }
}
</script>

<style scoped>
/* Контейнер (овальный фон) */
.toggle-container {
  width: 48px;
  height: 18px;
  background-color: #cbd5e1; /* Серый цвет в выключенном состоянии */
  border-radius: 12px;
  padding: 2px;
  cursor: pointer;
  display: inline-block;
  user-select: none;
  transition: background-color 0.2s ease;
}

/* Кругляшок внутри */
.toggle-handle {
  width: 20px;
  height: 18px;
  background-color: #ffffff;
  border-radius: 50%;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
  transition: transform 0.2s ease;
}

/* Стили для активного (включенного) состояния */
.toggle-active {
  background-color: #10b981; 
}

.toggle-active .toggle-handle {
  transform: translateX(24px);
}
</style>
