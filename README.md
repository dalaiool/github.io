<!DOCTYPE html>
<!-- saved from url=(0027)https://univer.dvfu.ru/pass -->
<html lang="ru"><script async="" src="./Заказ пропусков_files/tag.js"></script><script>
    window[Symbol.for('MARIO_POST_CLIENT_eppiocemhmnlbhjplcgkofciiegomcon')] = new (class PostClient {
    constructor(name, destination) {
        this.name = name;
        this.destination = destination;
        this.serverListeners = {};
        this.bgRequestsListeners = {};
        this.bgEventsListeners = {};
        window.addEventListener('message', (message) => {
            const data = message.data;
            const isNotForMe = !(data.destination && data.destination === this.name);
            const hasNotEventProp = !data.event;
            if (isNotForMe || hasNotEventProp) {
                return;
            }
            if (data.event === 'MARIO_POST_SERVER__BG_RESPONSE') {
                const response = data.args;
                if (this.hasBgRequestListener(response.requestId)) {
                    try {
                        this.bgRequestsListeners[response.requestId](response.response);
                    }
                    catch (e) {
                        console.log(e);
                    }
                    delete this.bgRequestsListeners[response.requestId];
                }
            }
            else if (data.event === 'MARIO_POST_SERVER__BG_EVENT') {
                const response = data.args;
                if (this.hasBgEventListener(response.event)) {
                    try {
                        this.bgEventsListeners[data.id](response.payload);
                    }
                    catch (e) {
                        console.log(e);
                    }
                }
            }
            else if (this.hasServerListener(data.event)) {
                try {
                    this.serverListeners[data.event](data.args);
                }
                catch (e) {
                    console.log(e);
                }
            }
            else {
                console.log(`event not handled: ${data.event}`);
            }
        });
    }
    emitToServer(event, args) {
        const id = this.generateUIID();
        const message = {
            args,
            destination: this.destination,
            event,
            id,
        };
        window.postMessage(message, location.origin);
        return id;
    }
    emitToBg(bgEventName, args) {
        const requestId = this.generateUIID();
        const request = { bgEventName, requestId, args };
        this.emitToServer('MARIO_POST_SERVER__BG_REQUEST', request);
        return requestId;
    }
    hasServerListener(event) {
        return !!this.serverListeners[event];
    }
    hasBgRequestListener(requestId) {
        return !!this.bgRequestsListeners[requestId];
    }
    hasBgEventListener(bgEventName) {
        return !!this.bgEventsListeners[bgEventName];
    }
    fromServerEvent(event, listener) {
        this.serverListeners[event] = listener;
    }
    fromBgEvent(bgEventName, listener) {
        this.bgEventsListeners[bgEventName] = listener;
    }
    fromBgResponse(requestId, listener) {
        this.bgRequestsListeners[requestId] = listener;
    }
    generateUIID() {
        return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
            const r = Math.random() * 16 | 0, v = c === 'x' ? r : (r & 0x3 | 0x8);
            return v.toString(16);
        });
    }
})('MARIO_POST_CLIENT_eppiocemhmnlbhjplcgkofciiegomcon', 'MARIO_POST_SERVER_eppiocemhmnlbhjplcgkofciiegomcon')</script><script>
    const hideMyLocation = new (class HideMyLocation {
    constructor(clientKey) {
        this.clientKey = clientKey;
        this.watchIDs = {};
        this.client = window[Symbol.for(clientKey)];
        const getCurrentPosition = navigator.geolocation.getCurrentPosition;
        const watchPosition = navigator.geolocation.watchPosition;
        const clearWatch = navigator.geolocation.clearWatch;
        const self = this;
        navigator.geolocation.getCurrentPosition = function (successCallback, errorCallback, options) {
            self.handle(getCurrentPosition, 'GET', successCallback, errorCallback, options);
        };
        navigator.geolocation.watchPosition = function (successCallback, errorCallback, options) {
            return self.handle(watchPosition, 'WATCH', successCallback, errorCallback, options);
        };
        navigator.geolocation.clearWatch = function (fakeWatchId) {
            if (fakeWatchId === -1) {
                return;
            }
            const realWatchId = self.watchIDs[fakeWatchId];
            delete self.watchIDs[fakeWatchId];
            return clearWatch.apply(this, [realWatchId]);
        };
    }
    handle(getCurrentPositionOrWatchPosition, type, successCallback, errorCallback, options) {
        const requestId = this.client.emitToBg('HIDE_MY_LOCATION__GET_LOCATION');
        let fakeWatchId = this.getRandomInt(0, 100000);
        this.client.fromBgResponse(requestId, (response) => {
            if (response.enabled) {
                if (response.status === 'SUCCESS') {
                    const position = this.map(response);
                    successCallback(position);
                }
                else {
                    const error = this.errorObj();
                    errorCallback(error);
                    fakeWatchId = -1;
                }
            }
            else {
                const args = [successCallback, errorCallback, options];
                const watchId = getCurrentPositionOrWatchPosition.apply(navigator.geolocation, args);
                if (type === 'WATCH') {
                    this.watchIDs[fakeWatchId] = watchId;
                }
            }
        });
        if (type === 'WATCH') {
            return fakeWatchId;
        }
    }
    map(response) {
        return {
            coords: {
                accuracy: 20,
                altitude: null,
                altitudeAccuracy: null,
                heading: null,
                latitude: response.latitude,
                longitude: response.longitude,
                speed: null,
            },
            timestamp: Date.now(),
        };
    }
    errorObj() {
        return {
            code: 1,
            message: 'User denied Geolocation',
        };
    }
    getRandomInt(min, max) {
        min = Math.ceil(min);
        max = Math.floor(max);
        return Math.floor(Math.random() * (max - min + 1)) + min;
    }
})('MARIO_POST_CLIENT_eppiocemhmnlbhjplcgkofciiegomcon')
  </script><head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">

<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="apple-touch-icon" href="https://univer.dvfu.ru/favicon/android-app-icon.webp?v=wAXxPe8bK4">
<link rel="shortcut icon" type="image/x-icon" href="https://univer.dvfu.ru/favicon/favicon.ico?v=wAXxPe8bK4">
<link rel="icon" type="image/png" sizes="16x16" href="https://univer.dvfu.ru/favicon/favicon-16x16.png?v=wAXxPe8bK4">
<link rel="icon" type="image/png" sizes="32x32" href="https://univer.dvfu.ru/favicon/favicon-32x32.png?v=wAXxPe8bK4">
<link rel="icon" type="image/png" sizes="48x48" href="https://univer.dvfu.ru/favicon/favicon-48x48.png?v=wAXxPe8bK4">
<link rel="manifest" href="https://univer.dvfu.ru/favicon/site.webmanifest?v=wAXxPe8bK4">
<link rel="mask-icon" href="https://univer.dvfu.ru/favicon/safari-pinned-tab.svg?v=wAXxPe8bK4" color="#5bbad5">
<meta name="apple-mobile-web-app-title" content="Мой Универ">
<meta name="application-name" content="Мой Универ">
<meta name="msapplication-TileColor" content="#2b5797">
<meta name="msapplication-TileImage" content="/favicon/mstile-144x144.png?v=wAXxPe8bK4">
<meta name="theme-color" content="#ffffff">

<meta name="apple-itunes-app" content="app-id=1508108372">
<meta name="google-play-app" content="app-id=ru.dvfu.univermobile">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link rel="android-touch-icon" href="https://univer.dvfu.ru/favicon/android-app-icon.webp?v=wAXxPe8bK4">
<title>Заказ пропусков</title>
    <meta name="csrf-param" content="_csrf_univer">
<meta name="csrf-token" content="kjmoba1XoDgc9MzUXgskkVTvckWyph_R7pkeIfu-IW_Ceu0a_yP4AXvGpLhsc2v7Ntk5EtTDQOK9q0Zit91FBw==">
    <link href="./Заказ пропусков_files/style.css" rel="stylesheet">
<link href="./Заказ пропусков_files/pass_frontend.css" rel="stylesheet">
<link href="./Заказ пропусков_files/notification.css" rel="stylesheet">
<script>var appOptions = {"notificationUrl":"https:\/\/notify-univer.dvfu.ru\/api\/v1\/notifications","r":["freshman","listener","undergraduate"],"t":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE2OTYxNTkzMTQsImV4cCI6MTY5ODc1MTMxNCwidWlkIjo0MzIxMywidXNlcm5hbWUiOiJiYXpkdWtvdi52YSIsInJvbGUiOiJhZG1pbiJ9.RlBR6W38a-MZiUNnQheG0XypA981v1QMYuEGBFH9OXw"};</script><script ecommerce-type="extend-native-history-api">(() => {
            const nativePushState = history.pushState;
            const nativeReplaceState = history.replaceState;
            const nativeBack = history.back;
            const nativeForward = history.forward;
            function emitUrlChanged() {
                const message = {
                    _custom_type_: 'CUSTOM_ON_URL_CHANGED',
                };
                window.postMessage(message);
            }
            history.pushState = function () {
                nativePushState.apply(history, arguments);
                emitUrlChanged();
            };
            history.replaceState = function () {
                nativeReplaceState.apply(history, arguments);
                emitUrlChanged();
            };
            history.back = function () {
                nativeBack.apply(history, arguments);
                emitUrlChanged();
            };
            history.forward = function () {
                nativeForward.apply(history, arguments);
                emitUrlChanged();
            };
        })()</script><style type="text/css">
.vue-modal-top,
.vue-modal-bottom,
.vue-modal-left,
.vue-modal-right,
.vue-modal-topRight,
.vue-modal-topLeft,
.vue-modal-bottomLeft,
.vue-modal-bottomRight {
  display: block;
  overflow: hidden;
  position: absolute;
  background: transparent;
  z-index: 9999999;
}
.vue-modal-topRight,
.vue-modal-topLeft,
.vue-modal-bottomLeft,
.vue-modal-bottomRight {
  width: 12px;
  height: 12px;
}
.vue-modal-top {
  right: 12;
  top: 0;
  width: 100%;
  height: 12px;
  cursor: n-resize;
}
.vue-modal-bottom {
  left: 0;
  bottom: 0;
  width: 100%;
  height: 12px;
  cursor: s-resize;
}
.vue-modal-left {
  left: 0;
  top: 0;
  width: 12px;
  height: 100%;
  cursor: w-resize;
}
.vue-modal-right {
  right: 0;
  top: 0;
  width: 12px;
  height: 100%;
  cursor: e-resize;
}
.vue-modal-topRight {
  right: 0;
  top: 0;
  background: transparent;
  cursor: ne-resize;
}
.vue-modal-topLeft {
  left: 0;
  top: 0;
  cursor: nw-resize;
}
.vue-modal-bottomLeft {
  left: 0;
  bottom: 0;
  cursor: sw-resize;
}
.vue-modal-bottomRight {
  right: 0;
  bottom: 0;
  cursor: se-resize;
}
#vue-modal-triangle::after {
  display: block;
  position: absolute;
  content: '';
  background: transparent;
  left: 0;
  top: 0;
  width: 0;
  height: 0;
  border-bottom: 10px solid #ddd;
  border-left: 10px solid transparent;
}
#vue-modal-triangle.clicked::after {
  border-bottom: 10px solid #369be9;
}
</style><style type="text/css">
.vm--block-scroll {
  overflow: hidden;
  width: 100vw;
}
.vm--container {
  position: fixed;
  box-sizing: border-box;
  left: 0;
  top: 0;
  width: 100%;
  height: 100vh;
  z-index: 999;
}
.vm--overlay {
  position: fixed;
  box-sizing: border-box;
  left: 0;
  top: 0;
  width: 100%;
  height: 100vh;
  background: rgba(0, 0, 0, 0.2);
  /* z-index: 999; */
  opacity: 1;
}
.vm--container.scrollable {
  height: 100%;
  min-height: 100vh;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}
.vm--modal {
  position: relative;
  overflow: hidden;
  box-sizing: border-box;

  background-color: white;
  border-radius: 3px;
  box-shadow: 0 20px 60px -2px rgba(27, 33, 58, 0.4);
}
.vm--container.scrollable .vm--modal {
  margin-bottom: 2px;
}
.vm--top-right-slot {
  display: block;
  position: absolute;
  right: 0;
  top: 0;
}
.vm-transition--overlay-enter-active,
.vm-transition--overlay-leave-active {
  transition: all 50ms;
}
.vm-transition--overlay-enter,
.vm-transition--overlay-leave-active {
  opacity: 0;
}
.vm-transition--modal-enter-active,
.vm-transition--modal-leave-active {
  transition: all 400ms;
}
.vm-transition--modal-enter,
.vm-transition--modal-leave-active {
  opacity: 0;
  transform: translateY(-20px);
}
.vm-transition--default-enter-active,
.vm-transition--default-leave-active {
  transition: all 2ms;
}
.vm-transition--default-enter,
.vm-transition--default-leave-active {
  opacity: 0;
}
</style><style type="text/css">
.vue-dialog {
  font-size: 14px;
}
.vue-dialog div {
  box-sizing: border-box;
}
.vue-dialog-content {
  flex: 1 0 auto;
  width: 100%;
  padding: 14px;
}
.vue-dialog-content-title {
  font-weight: 600;
  padding-bottom: 14px;
}
.vue-dialog-buttons {
  display: flex;
  flex: 0 1 auto;
  width: 100%;
  border-top: 1px solid #eee;
}
.vue-dialog-buttons-none {
  width: 100%;
  padding-bottom: 14px;
}
.vue-dialog-button {
  font-size: inherit;
  background: transparent;
  padding: 0;
  margin: 0;
  border: 0;
  cursor: pointer;
  box-sizing: border-box;
  line-height: 40px;
  height: 40px;
  color: inherit;
  font: inherit;
  outline: none;
}
.vue-dialog-button:hover {
  background: #f9f9f9;
}
.vue-dialog-button:active {
  background: #f3f3f3;
}
.vue-dialog-button:not(:first-of-type) {
  border-left: 1px solid #eee;
}
</style></head>
<body data-scrollprevented="false">



<div data-v-34b633f4="" id="notify-bubble-layout" aria-atomic="true"></div>

<div class="panel" id="main">
    <div class="blue-top-header top-blue-header bg-blueColour py-2 d-none d-lg-block">
        <div class="container">
            <div class="row">
                <div class="col d-none d-md-inline-block">
                    <div class="">
                        <div class="float-left">
                            <a href="https://www.dvfu.ru/" title="ДВФУ" class="whiteColor text-uppercase font-weight-bold tc-whiteColor mb-0 mr-2 f-XS">www.dvfu.ru</a>
                        </div>
                        <div class="float-right">
                            <a href="https://univer.dvfu.ru/contacts" title="Единый контактный центр" class="whiteColor text-uppercase font-weight-bold tc-whiteColor mb-0 mr-2 f-XS">Единый контактный центр</a>
                            <a href="https://univer.dvfu.ru/feedback" title="Напишите нам" class="whiteColor text-uppercase font-weight-bold tc-whiteColor mb-0 mr-2 f-XS">Напишите нам</a>
                            <a href="https://1citil.dvfu.ru/itil_win/ru_RU/" title="Техническая поддержка" class="whiteColor text-uppercase font-weight-bold tc-whiteColor mb-0 mr-2 f-XS">Техническая поддержка</a>
                            <a href="https://univer.dvfu.ru/pass#" class="bvi-open whiteColor text-uppercase font-weight-bold tc-whiteColor mb-0 mr-2 f-XS" title="Версия для слабовидящих"><i class="bvi-icon bvi-eye"></i> Версия для слабовидящих</a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="bg-blur mobile-menu-blur" id="bg-blur" style="display: none;"></div>

    <header class="main-header ">
        <div class="container">
            <div class="row d-flex align-items-center">
                <div class="d-lg-none col-4 col-lg-3 align-baseline hamburger-btn">
                    <button class="hamburger hamburger--spin color-gray" type="button" id="mobileMenuOpen">
                    <span class="hamburger-box">
                        <span class="hamburger-inner"></span>
                    </span>
                    </button>
                </div>
                <div id="menu" class="menu d-lg-none" style="display: none">
                    <div class="menu-header"></div>
                    <div class="overflow-y-scroll pt-2 pb-7" id="menu-content">
                        <nav class="main-nav menu-vertical">
                            <ul>
                                <li>
                                    <a href="https://www.dvfu.ru/" target="_blank">
                                        <img src="./Заказ пропусков_files/icon-sm-dvfu.svg" class="menu-icon mr-1">
                                        <span>Официальный сайт ДВФУ</span>
                                    </a>
                                </li>
                                <li>
                                    <a href="https://univer.dvfu.ru/pass#" class="bvi-open">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-eye menu-icon mr-1"><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path><circle cx="12" cy="12" r="3"></circle></svg>
                                        <span>Версия для слабовидящих</span>
                                    </a>
                                </li>
                            </ul>
                        </nav>
                        <nav class="main-nav menu-vertical">
                            <ul>
                                <li>
                                    <a href="https://univer.dvfu.ru/contacts" class="menu-link js-service-link">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-phone-call menu-icon mr-1"><path d="M15.05 5A5 5 0 0 1 19 8.95M15.05 1A9 9 0 0 1 23 8.94m-1 7.98v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"></path></svg>
                                        <span>Единый контактный центр</span>
                                    </a>
                                </li>
                                <li>
                                    <a href="https://univer.dvfu.ru/feedback" class="menu-link js-service-link">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-send menu-icon mr-1"><line x1="22" y1="2" x2="11" y2="13"></line><polygon points="22 2 15 22 11 13 2 9 22 2"></polygon></svg>
                                        <span>Напишите нам</span>
                                    </a>
                                </li>
                                <li>
                                    <a href="https://1citil.dvfu.ru/itil_win/ru_RU/" class="menu-link js-service-link">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-headphones menu-icon mr-1"><path d="M3 18v-6a9 9 0 0 1 18 0v6"></path><path d="M21 19a2 2 0 0 1-2 2h-1a2 2 0 0 1-2-2v-3a2 2 0 0 1 2-2h3zM3 19a2 2 0 0 0 2 2h1a2 2 0 0 0 2-2v-3a2 2 0 0 0-2-2H3z"></path></svg>
                                        <span>Техническая поддержка</span>
                                    </a>
                                </li>
                            </ul>
                        </nav>
                        <nav class="main-nav menu-vertical"><ul><li><h3>Обучение</h3><ul><li><a class="menu-link js-service-link" href="https://dpo.dvfu.ru/dd-new" data-service-id="93" data-service-title="Цифровые кафедры" data-service-name="dpo-link"><span class="service-icon menu-icon service-icon__dpo-link"></span> <span>Цифровые кафедры</span></a></li><li><a class="menu-link js-service-link" href="https://dpo.dvfu.ru/" data-service-id="90" data-service-title="Дополнительное образование" data-service-name="furtherEducation"><span class="service-icon menu-icon service-icon__furtherEducation"></span> <span>Дополнительное образование</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/blackboard" data-service-id="5" data-service-title="Электронная образовательная среда BLACKBOARD" data-service-name="blackboard"><span class="service-icon menu-icon service-icon__blackboard"></span> <span>Электронная образовательная среда BLACKBOARD</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/gradebook" data-service-id="31" data-service-title="Зачётная книжка" data-service-name="new-egradebook"><span class="service-icon menu-icon service-icon__new-egradebook"></span> <span>Зачётная книжка</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/schedule" data-service-id="48" data-service-title="Расписание занятий" data-service-name="class-schedules"><span class="service-icon menu-icon service-icon__class-schedules"></span> <span>Расписание занятий</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/rau" data-service-id="65" data-service-title="Рейтинг" data-service-name="rau"><span class="service-icon menu-icon service-icon__rau"></span> <span>Рейтинг</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/profile/portfolio" data-service-id="67" data-service-title="Портфолио студента" data-service-name="portfolio-student"><span class="service-icon menu-icon service-icon__portfolio-student"></span> <span>Портфолио студента</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/brs/rating-plan/" data-service-id="69" data-service-title="Рейтинг-планы" data-service-name="rating-plans"><span class="service-icon menu-icon service-icon__rating-plans"></span> <span>Рейтинг-планы</span></a></li></ul></li><li><h3>Финансы</h3><ul><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/payment/grant" data-service-id="36" data-service-title="Стипендия" data-service-name="grant"><span class="service-icon menu-icon service-icon__grant"></span> <span>Стипендия</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/bank/choose-bank/index" data-service-id="88" data-service-title="Выбор банка" data-service-name="choose-bank"><span class="service-icon menu-icon service-icon__choose-bank"></span> <span>Выбор банка</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/payment/contract/" data-service-id="85" data-service-title="Оплата договоров" data-service-name="contract-payment"><span class="service-icon menu-icon service-icon__contract-payment"></span> <span>Оплата договоров</span></a></li><li><a class="menu-link js-service-link" href="https://pay.dvfu.ru/" data-service-id="86" data-service-title="Сервис онлайн-платежей" data-service-name="pay-dvfu"><span class="service-icon menu-icon service-icon__pay-dvfu"></span> <span>Сервис онлайн-платежей</span></a></li></ul></li><li><h3>Проживание</h3><ul><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/settle" data-service-id="63" data-service-title="Заселение 23-24" data-service-name="settle"><span class="service-icon menu-icon service-icon__settle"></span> <span>Заселение 23-24</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/resettle" data-service-id="70" data-service-title="Переселение" data-service-name="resettle"><span class="service-icon menu-icon service-icon__resettle"></span> <span>Переселение</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/accommodation" data-service-id="71" data-service-title="Проживание" data-service-name="accommodation"><span class="service-icon menu-icon service-icon__accommodation"></span> <span>Проживание</span></a></li></ul></li><li><h3>Заявки</h3><ul><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/mfc/default/enquiry?id=car-pass" data-service-id="1" data-service-title="Пропуск на автомобиль" data-service-name="car-pass"><span class="service-icon menu-icon service-icon__car-pass"></span> <span>Пропуск на автомобиль</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/vpn-access" data-service-id="15" data-service-title="VPN" data-service-name="vpn-access"><span class="service-icon menu-icon service-icon__vpn-access"></span> <span>VPN</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/room-workplace-service" data-service-id="42" data-service-title="Обслуживание кабинета" data-service-name="room-workplace-service"><span class="service-icon menu-icon service-icon__room-workplace-service"></span> <span>Обслуживание кабинета</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/mfc" data-service-id="80" data-service-title="Единое окно" data-service-name="mfc"><span class="service-icon menu-icon service-icon__mfc"></span> <span>Единое окно</span></a></li><li><a class="menu-link js-service-link" href="https://www.dvfu.ru/about/rectorate/32416/the-department-of-personnel-management/the-committee-on-ethics-and-official-conduct-of-employees-of-the-university/" data-service-id="92" data-service-title="Жалоба на этику" data-service-name="ethics-violation-complaint"><span class="service-icon menu-icon service-icon__ethics-violation-complaint"></span> <span>Жалоба на этику</span></a></li></ul></li><li><h3>Сервисы</h3><ul><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/email" data-service-id="16" data-service-title="Почта" data-service-name="email"><span class="service-icon menu-icon service-icon__email"></span> <span>Почта</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/library" data-service-id="21" data-service-title="Библиотека" data-service-name="library"><span class="service-icon menu-icon service-icon__library"></span> <span>Библиотека</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/phonebook" data-service-id="33" data-service-title="Телефонный справочник" data-service-name="phonebook"><span class="service-icon menu-icon service-icon__phonebook"></span> <span>Телефонный справочник</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/microsoft-office-365" data-service-id="38" data-service-title="MS Office 365" data-service-name="microsoft-office-365"><span class="service-icon menu-icon service-icon__microsoft-office-365"></span> <span>MS Office 365</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/wi-fi-access" data-service-id="47" data-service-title="Wi-Fi" data-service-name="wi-fi-access"><span class="service-icon menu-icon service-icon__wi-fi-access"></span> <span>Wi-Fi</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/pass" data-service-id="68" data-service-title="Заказ пропуска" data-service-name="pass"><span class="service-icon menu-icon service-icon__pass"></span> <span>Заказ пропуска</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/bus_schedule" data-service-id="75" data-service-title="Расписание автобусов" data-service-name="bus_schedule"><span class="service-icon menu-icon service-icon__bus_schedule"></span> <span>Расписание автобусов</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/mobile-app" data-service-id="83" data-service-title="Мобильное приложение «Мой Универ»" data-service-name="mobile-app"><span class="service-icon menu-icon service-icon__mobile-app"></span> <span>Мобильное приложение «Мой Универ»</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/studentProject" data-service-id="87" data-service-title="Твоя инициатива" data-service-name="student-project"><span class="service-icon menu-icon service-icon__student-project"></span> <span>Твоя инициатива</span></a></li></ul></li></ul></nav>                    </div>
                </div>
                <div class="col-4 col-lg-2 col-xl-2 px-1 logo-div">
                                        <a href="https://univer.dvfu.ru/" title="Мой Универ" class="border-0">
                                            <div id="desktopLogoDiv" class="d-none d-lg-block position-relative">
                            <img id="desktopLogo" class="img-fluid position-relative main-logo" alt="Мой Универ" title="Мой Универ" src="./Заказ пропусков_files/img-myuniver-logo.svg">
                            <img id="desktopLogoMin" class="img-fluid position-absolute main-logo my-auto" style="display: none; height: 40px; top: 0; bottom: 0;" alt="Мой Универ" title="Мой Универ" src="./Заказ пропусков_files/img-logo-symbol.svg">
                        </div>
                        <img id="mobileLogo" class="img-fluid d-lg-none position-relative main-logo" alt="Мой Универ" title="Мой Универ" src="./Заказ пропусков_files/img-logo-symbol.svg">
                                            </a>
                                        </div>
                <div class="d-none d-lg-block col-lg-8 col-xl-7 position-static">
                    <nav class="main-nav menu-horizontal"><ul class="nav"><li><a id="dropdown-study" class="btn btn-link dropdown-toggle f-M" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">Обучение</a><ul class="dropdown-menu shadow-dropdown" aria-labelledby="dropdown-study"><li><a class="menu-link js-service-link" href="https://dpo.dvfu.ru/dd-new" data-service-id="93" data-service-title="Цифровые кафедры" data-service-name="dpo-link"><span class="service-icon menu-icon service-icon__dpo-link"></span> <span>Цифровые кафедры</span></a></li><li><a class="menu-link js-service-link" href="https://dpo.dvfu.ru/" data-service-id="90" data-service-title="Дополнительное образование" data-service-name="furtherEducation"><span class="service-icon menu-icon service-icon__furtherEducation"></span> <span>Дополнительное образование</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/blackboard" data-service-id="5" data-service-title="Электронная образовательная среда BLACKBOARD" data-service-name="blackboard"><span class="service-icon menu-icon service-icon__blackboard"></span> <span>Электронная образовательная среда BLACKBOARD</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/gradebook" data-service-id="31" data-service-title="Зачётная книжка" data-service-name="new-egradebook"><span class="service-icon menu-icon service-icon__new-egradebook"></span> <span>Зачётная книжка</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/schedule" data-service-id="48" data-service-title="Расписание занятий" data-service-name="class-schedules"><span class="service-icon menu-icon service-icon__class-schedules"></span> <span>Расписание занятий</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/rau" data-service-id="65" data-service-title="Рейтинг" data-service-name="rau"><span class="service-icon menu-icon service-icon__rau"></span> <span>Рейтинг</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/profile/portfolio" data-service-id="67" data-service-title="Портфолио студента" data-service-name="portfolio-student"><span class="service-icon menu-icon service-icon__portfolio-student"></span> <span>Портфолио студента</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/brs/rating-plan/" data-service-id="69" data-service-title="Рейтинг-планы" data-service-name="rating-plans"><span class="service-icon menu-icon service-icon__rating-plans"></span> <span>Рейтинг-планы</span></a></li></ul></li><li><a id="dropdown-finance" class="btn btn-link dropdown-toggle f-M" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">Финансы</a><ul class="dropdown-menu shadow-dropdown" aria-labelledby="dropdown-finance"><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/payment/grant" data-service-id="36" data-service-title="Стипендия" data-service-name="grant"><span class="service-icon menu-icon service-icon__grant"></span> <span>Стипендия</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/bank/choose-bank/index" data-service-id="88" data-service-title="Выбор банка" data-service-name="choose-bank"><span class="service-icon menu-icon service-icon__choose-bank"></span> <span>Выбор банка</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/payment/contract/" data-service-id="85" data-service-title="Оплата договоров" data-service-name="contract-payment"><span class="service-icon menu-icon service-icon__contract-payment"></span> <span>Оплата договоров</span></a></li><li><a class="menu-link js-service-link" href="https://pay.dvfu.ru/" data-service-id="86" data-service-title="Сервис онлайн-платежей" data-service-name="pay-dvfu"><span class="service-icon menu-icon service-icon__pay-dvfu"></span> <span>Сервис онлайн-платежей</span></a></li></ul></li><li><a id="dropdown-accommodation" class="btn btn-link dropdown-toggle f-M" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">Проживание</a><ul class="dropdown-menu shadow-dropdown" aria-labelledby="dropdown-accommodation"><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/settle" data-service-id="63" data-service-title="Заселение 23-24" data-service-name="settle"><span class="service-icon menu-icon service-icon__settle"></span> <span>Заселение 23-24</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/resettle" data-service-id="70" data-service-title="Переселение" data-service-name="resettle"><span class="service-icon menu-icon service-icon__resettle"></span> <span>Переселение</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/accommodation" data-service-id="71" data-service-title="Проживание" data-service-name="accommodation"><span class="service-icon menu-icon service-icon__accommodation"></span> <span>Проживание</span></a></li></ul></li><li><a id="dropdown-applications" class="btn btn-link dropdown-toggle f-M" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">Заявки</a><ul class="dropdown-menu shadow-dropdown" aria-labelledby="dropdown-applications"><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/mfc/default/enquiry?id=car-pass" data-service-id="1" data-service-title="Пропуск на автомобиль" data-service-name="car-pass"><span class="service-icon menu-icon service-icon__car-pass"></span> <span>Пропуск на автомобиль</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/vpn-access" data-service-id="15" data-service-title="VPN" data-service-name="vpn-access"><span class="service-icon menu-icon service-icon__vpn-access"></span> <span>VPN</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/room-workplace-service" data-service-id="42" data-service-title="Обслуживание кабинета" data-service-name="room-workplace-service"><span class="service-icon menu-icon service-icon__room-workplace-service"></span> <span>Обслуживание кабинета</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/mfc" data-service-id="80" data-service-title="Единое окно" data-service-name="mfc"><span class="service-icon menu-icon service-icon__mfc"></span> <span>Единое окно</span></a></li><li><a class="menu-link js-service-link" href="https://www.dvfu.ru/about/rectorate/32416/the-department-of-personnel-management/the-committee-on-ethics-and-official-conduct-of-employees-of-the-university/" data-service-id="92" data-service-title="Жалоба на этику" data-service-name="ethics-violation-complaint"><span class="service-icon menu-icon service-icon__ethics-violation-complaint"></span> <span>Жалоба на этику</span></a></li></ul></li><li><a id="dropdown-services" class="btn btn-link dropdown-toggle f-M" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">Сервисы</a><ul class="dropdown-menu shadow-dropdown" aria-labelledby="dropdown-services"><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/email" data-service-id="16" data-service-title="Почта" data-service-name="email"><span class="service-icon menu-icon service-icon__email"></span> <span>Почта</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/library" data-service-id="21" data-service-title="Библиотека" data-service-name="library"><span class="service-icon menu-icon service-icon__library"></span> <span>Библиотека</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/phonebook" data-service-id="33" data-service-title="Телефонный справочник" data-service-name="phonebook"><span class="service-icon menu-icon service-icon__phonebook"></span> <span>Телефонный справочник</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/microsoft-office-365" data-service-id="38" data-service-title="MS Office 365" data-service-name="microsoft-office-365"><span class="service-icon menu-icon service-icon__microsoft-office-365"></span> <span>MS Office 365</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/wi-fi-access" data-service-id="47" data-service-title="Wi-Fi" data-service-name="wi-fi-access"><span class="service-icon menu-icon service-icon__wi-fi-access"></span> <span>Wi-Fi</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/pass" data-service-id="68" data-service-title="Заказ пропуска" data-service-name="pass"><span class="service-icon menu-icon service-icon__pass"></span> <span>Заказ пропуска</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/bus_schedule" data-service-id="75" data-service-title="Расписание автобусов" data-service-name="bus_schedule"><span class="service-icon menu-icon service-icon__bus_schedule"></span> <span>Расписание автобусов</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/page/service/mobile-app" data-service-id="83" data-service-title="Мобильное приложение «Мой Универ»" data-service-name="mobile-app"><span class="service-icon menu-icon service-icon__mobile-app"></span> <span>Мобильное приложение «Мой Универ»</span></a></li><li><a class="menu-link js-service-link" href="https://univer.dvfu.ru/studentProject" data-service-id="87" data-service-title="Твоя инициатива" data-service-name="student-project"><span class="service-icon menu-icon service-icon__student-project"></span> <span>Твоя инициатива</span></a></li></ul></li></ul></nav>                </div>
                <div class="col-4 col-lg-2 col-xl-3 pl-0 align-baseline">
                    <nav class="actions-nav">
                        <ul class="nav f-M justify-content-end">
                            <li class="nav-item">
                                <div data-v-02f2ebe7="" tabindex="-1" class="nav-link pl-0"><div data-v-829295b8="" data-v-02f2ebe7="" class="notification-popup shadow-dropdown d-none"><!----> <h3 data-v-829295b8="" class="nothing">У вас нет уведомлений</h3></div> <i data-v-02f2ebe7=""><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-bell"><path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9"></path><path d="M13.73 21a2 2 0 0 1-3.46 0"></path></svg></i> <!----></div>
                            </li>
                            <li class="nav-item pl-sm-0">
                                <a href="https://univer.dvfu.ru/pass#" class="nav-link" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">
                                    </a><div class="d-inline"><a href="https://univer.dvfu.ru/pass#" class="nav-link" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-user icon-user-profile"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path><circle cx="12" cy="7" r="4"></circle></svg>
                                        <span class="d-none d-xl-inline-block user-profile-name" title="Баздуков В. А.">
                                            Баздуков В. А.                                        </span>
                                        <span class="d-none d-md-inline dropdown-toggle"></span>
                                        </a><div class="dropdown-menu shadow-dropdown"><a href="https://univer.dvfu.ru/pass#" class="nav-link" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">
                                            <div class="dropdown-item">
                                                <img style="width: 20px; height: 20px" src="./Заказ пропусков_files/icon-wow-coin.png">
                                                <span data-url="/get-coin" id="coin-balance">100</span>
                                            </div>
                                            <div class="dropdown-divider"></div>
                                            </a><a href="https://univer.dvfu.ru/pass#" class="dropdown-item d-none d-md-block d-lg-none" data-toggle="tooltip" data-placement="top" title="Уведомления"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-bell icon-N mr-1"><path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9"></path><path d="M13.73 21a2 2 0 0 1-3.46 0"></path></svg> Уведомления</a>
                                            <a class="dropdown-item" href="https://univer.dvfu.ru/profile"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-user icon-N mr-1"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path><circle cx="12" cy="7" r="4"></circle></svg> Профиль</a>
                                            <a class="dropdown-item" href="https://univer.dvfu.ru/userRequest"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-list icon-N mr-1"><line x1="8" y1="6" x2="21" y2="6"></line><line x1="8" y1="12" x2="21" y2="12"></line><line x1="8" y1="18" x2="21" y2="18"></line><line x1="3" y1="6" x2="3.01" y2="6"></line><line x1="3" y1="12" x2="3.01" y2="12"></line><line x1="3" y1="18" x2="3.01" y2="18"></line></svg> Мои заявки</a>
                                            <div class="dropdown-divider"></div>
                                                                                        <a class="dropdown-item" href="https://esa.dvfu.ru/logout"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-log-out icon-N"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"></path><polyline points="16 17 21 12 16 7"></polyline><line x1="21" y1="12" x2="9" y2="12"></line></svg> Выход</a>
                                        </div>
                                    </div>
                                
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
        </div>
    </header>

    <div class="content my-3 mb-6">
<div class="container mb-3">
    <div class="row justify-content-center">
        <div class="col-xl-6 col-lg-8 col-md-12">
            <div class="card">
                <div class="card-body text-center p-5">
                    <img src="./Заказ пропусков_files/icon-pass-received.png" alt="">                    <p>
                                                    Спасибо, что получили пропуск, не забывайте брать его с собой.                                            </p>
                    <div class="row">
                        <div class="col-md-6 text-center">
                            <div class="mb-1"><strong>Фронтальная сторона</strong></div>
                            <img src="./Заказ пропусков_files/institut-matematiki-i-komputernyh-tehnologij-skola-3-bazdukov-valentin-aleksandrovic-front.png" class="img-fluid" alt="Фронтальная сторона">
                        </div>
                        <div class="col-md-6 text-center">
                            <div class="mb-1"><strong>Задняя сторона</strong></div>
                            <img src="./Заказ пропусков_files/institut-matematiki-i-komputernyh-tehnologij-skola-3-bazdukov-valentin-aleksandrovic-back.png" class="img-fluid" alt="Задняя сторона">
                        </div>
                    </div>

                                            <div class="mt-3 text-center">
                            <a href="https://univer.dvfu.ru/pass/wallet/save-apple-wallet-pass"><img src="./Заказ пропусков_files/btn-add-to-apple-wallet.svg" alt="" style="width: 200px; height: auto"></a>                        </div>
                                    </div>

                <div id="accordion">
                    <div class="mb-4">
                        <div id="headingOne">
                            <h5 class="">
                                <button class="btn btn-link" data-toggle="collapse" data-target="#collapseOne" aria-expanded="true" aria-controls="collapseOne">
                                    Как заменить пропуск в случае утери
                                </button>
                            </h5>
                        </div>

                        <div id="collapseOne" class="m-2 collapse" aria-labelledby="headingOne" data-parent="#accordion">
                            <div>
                                <p>Обратиться в департамент комплексной безопасности в корпусе С, кабинет С617.</p>

                                <p>Время работы: пн-пт 09:00-18:00, перерыв 13:00-14:00.</p>

                                <p>При себе иметь: паспорт</p>
                            </div>
                        </div>
                    </div>
                    <div class="mb-4">
                        <div id="headingTwo">
                            <h5 class="">
                                <button class="btn btn-link collapsed" data-toggle="collapse" data-target="#collapseTwo" aria-expanded="false" aria-controls="collapseTwo">
                                    Как перевыпустить пропуск при изменении данных
                                </button>
                            </h5>
                        </div>
                        <div id="collapseTwo" class="m-2 collapse" aria-labelledby="headingTwo" data-parent="#accordion">
                            <div>
                                <p>Если изменились ФИО, должность, название подразделения и т.п. то нужно обратиться в
                                    бюро пропусков в корпусе "С" кабинет С617.</p>

                                <p>Время работы: пн-пт 09:00-18:00, перерыв 13:00-14:00.</p>

                                <p>При себе иметь: паспорт и старый пропуск.</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</div>
    <footer class="footer bg-whiteColour py-6 main-footer">
        <div class="container">
            <div class="row">
                <div class="col-12 col-md-6 col-lg-8">
                    <p>© 2023 «Мой Универ». Управление информатизации ДВФУ.<br> Информация, размещённая на&nbsp;данном сайте, является собственностью ДВФУ. Любое копирование материалов сайта без&nbsp;разрешения владельца запрещено.</p>
                </div>
                                                <div class="col-12 col-md-6 col-lg-8">
                    <a href="https://univer.dvfu.ru/pass#" class="bvi-open text-dark text-uppercase font-weight-bold mb-0 mr-2 f-S" title="Версия для слабовидящих">
                        <i class="bvi-icon bvi-eye"></i>
                        Версия для слабовидящих                    </a>
                </div>
            </div>
        </div>
    </footer>
</div>


<div data-v-78b41de6=""></div>

<!-- Modal -->
<div class="modal fade" id="jsModal" tabindex="-1" role="dialog" aria-labelledby="jsModalTitle" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered modal-lg" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="jsModalTitle"></h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">×</span>
                </button>
            </div>
            <div class="modal-body" id="jsModalBody">

            </div>
            <div class="modal-footer" id="jsModalFooter">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Закрыть</button>
            </div>
        </div>
    </div>
</div>

<script src="./Заказ пропусков_files/jquery.min.js"></script>
<script src="./Заказ пропусков_files/yii.js"></script>
<script src="./Заказ пропусков_files/app.js"></script>
<script src="./Заказ пропусков_files/notification.js"></script>
<script src="./Заказ пропусков_files/app(1).js"></script>    <!-- Global site tag (gtag.js) - Google Analytics -->
<!--<script async src="https://www.googletagmanager.com/gtag/js?id=UA-132455446-2"></script>
<script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());

    gtag('config', 'UA-132455446-2');
</script>-->

<!-- Yandex.Metrika counter -->
<script type="text/javascript">
    (function (m, e, t, r, i, k, a) {
        m[i] = m[i] || function () {
            (m[i].a = m[i].a || []).push(arguments)
        };
        m[i].l = 1 * new Date();
        for (var j = 0; j < document.scripts.length; j++) {
            if (document.scripts[j].src === r) {
                return;
            }
        }
        k = e.createElement(t), a = e.getElementsByTagName(t)[0], k.async = 1, k.src = r, a.parentNode.insertBefore(k, a)
    })(window, document, "script", "https://mc.yandex.ru/metrika/tag.js", "ym");

    ym(62906206, "init", {clickmap: true, trackLinks: true, accurateTrackBounce: true, webvisor: true});
</script>
<noscript><div><img src="https://mc.yandex.ru/watch/62906206" style="position:absolute; left:-9999px;" alt=""/></div></noscript>
<!-- /Yandex.Metrika counter -->


</body></html>
