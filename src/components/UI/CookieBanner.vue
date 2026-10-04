<template>
  <!-- Баннер отображается только если visible === true -->
  <transition name="fade">
    <div v-if="visible" class="cookie-banner" role="alert">
      <div class="cookie-banner__content">
        <span class="cookie-banner__text">
          Продолжая использовать наш сайт, вы соглашаетесь на обработку файлов cookie 
          и технических данных сервисом Яндекс Метрика для улучшения работы генератора дат для КТП. 
          Вы можете отключить cookie в настройках браузера.
          <a href="/privacy.html" class="cookie-banner__link" target="_blank">
            Читать Политику конфиденциальности
          </a>
        </span>
        <button class="cookie-banner__btn" @click="acceptCookies">
          Принять
        </button>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'CookieBanner',
  data() {
    return {
      // По умолчанию скрыт, пока не проверим localStorage
      visible: false 
    };
  },
  mounted() {
    this.checkConsent();
  },
  methods: {
    // Проверяем, давал ли пользователь согласие ранее
    checkConsent() {
      const consent = localStorage.getItem('cookie_consent_accepted');
      if (!consent) {
        this.visible = true;
      }
    },
    // Запоминаем выбор и скрываем баннер
    acceptCookies() {
      localStorage.setItem('cookie_consent_accepted', 'true');
      this.visible = false;
    }
  }
};
</script>

<style scoped>
/* Контейнер баннера */
.cookie-banner {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  width: 90%;
  max-width: 600px;
  background-color: #2c3e50;
  color: #ffffff;
  padding: 16px 20px;
  border-radius: 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
  z-index: 9999;
}

/* Содержимое (текст + кнопка) */
.cookie-banner__content {
  display: flex;
  flex-direction: column;
  gap: 12px;
  align-items: flex-start;
  font-family: sans-serif;
  font-size: 14px;
  line-height: 1.4;
}

/* Ссылка на политику */
.cookie-banner__link {
  color: #42b983;
  text-decoration: underline;
  margin-left: 4px;
  white-space: nowrap;
}

/* Кнопка "Принять" */
.cookie-banner__btn {
  background-color: #42b983;
  color: #ffffff;
  border: none;
  padding: 8px 20px;
  font-size: 14px;
  font-weight: bold;
  border-radius: 4px;
  cursor: pointer;
  align-self: flex-end;
  transition: background-color 0.2s ease;
}

.cookie-banner__btn:hover {
  background-color: #35495e;
}

/* Адаптив для десктопов: выстраиваем в одну линию */
@media (min-width: 576px) {
  .cookie-banner__content {
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    gap: 20px;
  }
  
  .cookie-banner__btn {
    align-self: center;
  }
}

/* Анимация плавного появления и исчезновения */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease, transform 0.4s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translate(-50%, 20px);
}
</style>
