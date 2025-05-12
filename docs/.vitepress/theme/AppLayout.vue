<!-- eslint-disable no-restricted-globals -->
<script setup>
import DefaultTheme from "vitepress/theme";
import { onMounted } from "vue";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import { useData } from "vitepress";

const { site, page } = useData();
const filePath = page.value.filePath;

// console.log(useData());

onMounted(() => {

  // saveVisit(config);

  changeNavbar();
});

function changeNavbar() {
  const navItems = document.querySelectorAll('.VPNav .VPNavBarMenuLink');
  
  if (!navItems || navItems.length < 2) {
    return;
  }
  const targetItem = navItems[2];
  targetItem.style.color = 'var(--vp-c-green-1)';
  if (Math.random() > 0.5) {
        // Находим третий пункт меню в DOM (индекс 2)
        
        // Меняем содержимое пункта меню
        const link = targetItem.getAttribute('href');
        if (link && link !== 'https://startup-tools.ru/telegram-tools/noema?utm_source=vue-faq.org&utm_medium=link&utm_campaign=vue-faq-website') {
            targetItem.setAttribute('href', 'https://startup-tools.ru/telegram-tools/noema?utm_source=vue-faq.org&utm_medium=link&utm_campaign=vue-faq-website');
            targetItem.textContent = 'Noema';
            // menuChanged.value = true
            // console.log('Меню изменено через DOM')
        }
    }
}

async function saveVisit() {
  const searchParams = new URLSearchParams(window.location.search);
  if (searchParams.has("t")) {
    const tt = +searchParams.get("t") - 1;
    document.querySelectorAll("main details")[tt].open = true;
  }
  // let uuid = self.crypto.randomUUID();
  // console.log(uuid);

  let config = JSON.parse(localStorage.getItem("vue-faq-config"));
  // console.log(data);
  if (!config) {
    config = {
      version: 1.5,
      userId: self.crypto.randomUUID(),
      visits: 0,
      notifications: {
        telegram: true,
        githubStars: false,
      },
    };
    // localStorage.setItem("vue-faq-config", config);
  }
  config.visits++;
  config.version = 1.5;
  if (!config.userId) {
    config.userId = self.crypto.randomUUID();
  }
  if (!config.notifications.githubStars && (site.value.lang === "ru" || navigator.language === "ru-RU") && config.visits > 2) {
    // showTelegramNotification(localStorage, config);
  }
  localStorage.setItem("vue-faq-config", JSON.stringify(config));

  config.ip = "";
  try {
    const { ip } = await (await fetch("https://api64.ipify.org?format=json")).json();
    config.ip = ip;
  } catch (error) {
    console.log("ipe");
  }
  // .then((response) => response.json())
  // .then((json) => {
  config.referrer = document.referrer;
  config.filePath = filePath;
  localStorage.setItem("vue-faq-config", JSON.stringify(config));
  const url = "https://dev.ultravintage.net/misc/";
  const payload = JSON.stringify({
    userId: config.userId,
    data: config,
  });
  const options = {
    method: "POST",
    // body: '{"userId":"sfsfd-asdasdgfsffdfsfds-fs-","data":{"a":1,"b":"hell1o"}}'
    body: JSON.stringify({ data: `s${window.btoa(payload)}` }),
  };

  try {
    fetch(url, options)
    // .then((response) => response.json())
    // .then((response) => response.text)
    // .then((data) => {
    //     console.log(data);
    // })
    ;
    // const data = await response.json();
  } catch (error) {
    console.error(error);
  }
}

function showTelegramNotification(localStorage, config) {
  const str = `
    <h3>Уважаемые читатели</h3>
    За последние 30 дней согласно Google Analytics у нас было более 1500 регулярно заходящих пользователей сайта, и на данный момент всего 65 GitHub звезд. Данный показатель очень важен для open source проектов как обратная связь.

    У нас есть планы по развитию ресурса, и он никогда не будет платным, но если данный показатель останется на низком уровне, мы будем вынуждены пересмотреть планы и частично ограничить доступ.

    <h3 style="text-align: end;"><u><a target="_blank" href="https://github.com/vuesence/vue-faq">Поставить GitHub звезду</a></u></h3>
    `;
  toast(str, {
    autoClose: 20000,
    type: "info",
    delay: 500,
    dangerouslyHTMLString: true,
    // icon: "💬",
    transition: toast.TRANSITIONS.FLIP,
    position: toast.POSITION.BOTTOM_RIGHT,
    theme: "auto",
    onClose: () => {
      config.notifications.githubStars = true;
      localStorage.setItem("vue-faq-config", JSON.stringify(config));
    },
    onClick: () => {
      config.notifications.githubStars = true;
      localStorage.setItem("vue-faq-config", JSON.stringify(config));
    },
  });
}
</script>

<template>
  <DefaultTheme.Layout />
</template>

<style>
:root {
  --toastify-toast-width: 420px;
}
h3 {
    font-weight: 600;
    font-size: larger;
}
</style>
