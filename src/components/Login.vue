<template>
    <div id="login-wrapper" v-show="showLogin" ref="floatLogin">
        <div id="loginBar" @mousedown="dragLogin" @touchmove="dragLogin">
            <span style="margin-left: 10px; font-size: 1.5rem"
                >단어게임 접속</span
            >
            <img
                src="/assets/image/button/btn_close.png"
                style="
                    width: 30px;
                    height: 30px;
                    cursor: pointer;
                    margin-right: 10px;
                "
                alt=""
                @click="closeLogin()"
            />
        </div>
        <div id="alien"></div>
        <div id="loginbox">
            <div id="entrance">
                <div class="input_wrapper">
                    <input
                        id="input_room_id"
                        type="text"
                        v-model="room_code"
                        placeholder="입장코드 입력(영문, 숫자)"
                        :maxlength="6"
                        oninput="javascript: 
                        if ((/[^A-Za-z0-9]$/ig).test(this.value) == true) {
                            let inputValue = this.value
                            let deleteLast = inputValue.slice(0, -1)
                            this.value = deleteLast; 
                                                    } "
                    />
                </div>
                <div class="input_wrapper">
                    <input
                        id="input_user_id"
                        type="text"
                        v-model="user_id"
                        placeholder="닉네임 입력(영문, 숫자)"
                        :maxlength="6"
                        @keypress.enter="createPage"
                        oninput="javascript: 
                        if ((/[^A-Za-z0-9]$/ig).test(this.value) == true) {
                            let inputValue = this.value
                            let deleteLast = inputValue.slice(0, -1)
                            this.value = deleteLast; 
                                                    } "
                    />
                </div>
                <br />
                <button
                    @click="createPage"
                    id="join-buttom"
                    @mouseover="buttonHovered"
                >
                    입장
                </button>
            </div>
        </div>
    </div>
    <div class="modal-wrapper" id="modal-wrapper">
        <div class="modal" id="modal">
            <div class="modal-title" id="modal-title">경고</div>
            <div class="modal-content" id="modal-content">
                <div>영문과 숫자만 입력 가능합니다!</div>
                <div class="close_modal">
                    <button class="modal-close-button" id="close_modal">
                        확 인
                    </button>
                </div>
            </div>
        </div>
    </div>
    <div class="modal-wrapper" id="modal-wrapper2">
        <div class="modal" id="modal_2">
            <div class="modal-title" id="modal-title-2">경고</div>
            <div class="modal-content" id="modal-content-2">
                <div>입력값을 적어주세요</div>
                <div class="close_modal">
                    <button class="modal-close-button" id="close_modal_2">
                        확 인
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";

export default {
    props: {
        room_name: {
            type: String,
            required: false,
        },
    },
    data() {
        return {
            logPos1: 0,
            logPos2: 0,
            logPos3: 0,
            logPos4: 0,
            room_code: "",
        };
    },
    methods: {
        closeLogin() {
            const click_sound = new Audio(
                "/assets/soundEffect/basic_click.mp3"
            );
            click_sound.volume = 0.6;
            click_sound.play();
            this.showLogin = false;
        },
        openLogin() {
            this.$refs.floatLogin.style.top = "5%";
            this.$refs.floatLogin.style.left = "10%";
            this.showLogin = true;
        },
        dragLogin(e) {
            e = e || window.event;
            e.preventDefault();
            this.logPos3 = e.clientX;
            this.logPos4 = e.clientY;
            document.onmouseup = this.closeDragElement;
            document.onmousemove = this.loginDrag;
        },
        loginDrag(e) {
            e = e || window.event;
            e.preventDefault();
            this.logPos1 = this.logPos3 - e.clientX;
            this.logPos2 = this.logPos4 - e.clientY;
            this.logPos3 = e.clientX;
            this.logPos4 = e.clientY;
            this.$refs.floatLogin.style.top =
                Math.max(
                    Math.min(
                        this.$refs.floatLogin.offsetTop - this.logPos2,
                        window.innerHeight - this.$refs.floatLogin.style.height
                    ),
                    -150
                ) + "px";
            this.$refs.floatLogin.style.left =
                Math.max(
                    Math.min(
                        this.$refs.floatLogin.offsetLeft - this.logPos1,
                        window.innerWidth - this.$refs.floatLogin.style.width
                    ),
                    -500
                ) + "px";
        },
        closeDragElement() {
            document.onmouseup = null;
            document.onmousemove = null;
        },
        buttonHovered() {
            const water = new Audio("/assets/soundEffect/audio_water.mp3");
            water.volume = 0.3;
            water.play();
        },
    },
    setup() {
        let showLogin = ref(true);
        onMounted(() => {
            const closeModal = document.getElementById("close_modal");
            const closeModal2 = document.getElementById("close_modal_2");
            const modal = document.getElementById("modal-wrapper");
            const modal2 = document.getElementById("modal-wrapper2");
            closeModal.onclick = () => {
                modal.style.display = "none";
            };

            closeModal.addEventListener("keydown", function (e) {
                if (e.keyCode === 13) {
                    if (modal.style.display === "flex") {
                        modal.style.display = "none";
                    }
                }
            });
            closeModal2.onclick = () => {
                modal2.style.display = "none";
            };
            closeModal2.addEventListener("keydown", function (e) {
                if (e.keyCode === 13) {
                    if (modal2.style.display === "flex") {
                        modal2.style.display = "none";
                    }
                }
            });
        });
        const router = useRouter();
        const user_id = ref("");
        const audio_enter = new Audio("/assets/soundEffect/audio_enter.mp3");
        const audio_ding = new Audio("/assets/soundEffect/ding.wav");
        audio_enter.volume = 0.6;
        const joinPage = () => {
            const roomID = document.getElementById("input_room_id");
            const userID = document.getElementById("input_user_id");
            const modal2 = document.getElementById("modal-wrapper2");
            // if (userID.value == "" || roomID.value == "") {
            if (userID.value == "") {
                modal2.style.display = "flex";
                document.getElementById("close_modal_2").focus();
                audio_ding.play();
            } else {
                audio_enter.play();
                router.push({
                    name: "inGame",
                    params: {
                        room_code: roomID.value,
                    },
                    query: {
                        user_id: `${user_id.value}`,
                    },
                });
            }
        };
        const createPage = () => {
            const roomID = document.getElementById("input_room_id");
            const userID = document.getElementById("input_user_id");
            const modal2 = document.getElementById("modal-wrapper2");
            if (userID.value == "") {
                modal2.style.display = "flex";
                document.getElementById("close_modal_2").focus();
                audio_ding.play();
            } else {
                if (roomID.value == "") {
                    const new_room_code = generateRandomCode(6);
                    audio_enter.play();
                    router.push({
                        name: "inGame",
                        params: {
                            room_code: `${new_room_code}`,
                        },
                        query: {
                            user_id: `${user_id.value}`,
                        },
                    });
                }
                return joinPage();
            }
        };
        function generateRandomCode(n) {
            const characters = "abcdefghijklmnopqrstuvwxyz0123456789";
            let randomcode = "";
            const charactersLength = characters.length;
            for (let i = 0; i < n; i++) {
                randomcode += characters.charAt(
                    Math.floor(Math.random() * charactersLength)
                );
            }
            return randomcode;
        }
        return {
            user_id,
            joinPage,
            createPage,
            showLogin,
            generateRandomCode,
        };
    },
    mounted() {
        if (this.$route.query.room_code) {
            const input_room_id = document.getElementById("input_room_id");
            input_room_id.value = this.$route.query.room_code;
            this.room_code = this.$route.query.room_code;
        }
    },
};
</script>

<style scoped>
@keyframes alien {
    0% {
        background-image: url("/assets/image/game/alien_normal.svg");
    }
    49.9% {
        background-image: url("/assets/image/game/alien_normal.svg");
    }
    50.0% {
        background-image: url("/assets/image/game/alien_raise.svg");
    }
    100% {
        background-image: url("/assets/image/game/alien_raise.svg");
    }
}
@keyframes alien_bar {
    0% {
        box-shadow: 0 5px rgb(22, 255, 94);
    }
    100% {
        box-shadow: 0 5px rgb(255, 94, 0);
    }
}
#login-wrapper {
    position: absolute;
    box-sizing: border-box;
    top: 5%;
    right: 10%;
    width: 80%;
    height: 80%;
    background-color: #000;
    border-bottom: 2px solid rgb(180, 180, 180);
    border-right: 2px solid rgb(180, 180, 180);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

@media (max-width: 740px) {
    #alien {
        display: none;
    }
}
@media (max-height: 800px) {
    #alien {
        display: none;
    }
}
@media (max-width: 470px) {
    br {
        display: none;
    }
}
#alien {
    position: absolute;
    top: 15%;
    left: 40%;
    width: 17%;
    height: 12%;
    background-size: cover;
    animation: alien 1s linear infinite;
}
#alien:hover {
    animation: alien 1s linear infinite, alien_bar 1s alternate infinite;
}
#loginbox {
    width: 95%;
    height: 95%;
    display: flex;
    justify-content: center;
    align-items: center;
    border: 4px solid rgb(22, 255, 94);
}

#logo {
    /* height: 40%; */
    text-align: center;
}
#entrance {
    text-align: center;
    width: 100%;
    /* height: 50%; */
}
#input_room_id {
    margin: 5px;
    width: 40%;
    min-width: 30px;
    height: 50px;
    font-family: "DungGeunMo";
    font-size: large;
    text-align: center;
    background-color: transparent;
    border: 3px solid rgb(22, 255, 94);
    color: rgb(22, 255, 94);
}
#input_room_id::placeholder {
    color: rgb(11, 121, 46);
}
#input_room_id:focus {
    outline: none !important;
    border-color: rgb(22, 255, 94);
    box-shadow: 0 0 10px 5px rgb(22, 255, 94);
}
#input_user_id {
    margin: 5px;
    width: 40%;
    min-width: 30px;
    height: 50px;
    font-family: "DungGeunMo";
    font-size: large;
    text-align: center;
    background-color: transparent;
    border: 3px solid rgb(22, 255, 94);
    color: rgb(22, 255, 94);
}
#input_user_id::placeholder {
    color: rgb(11, 121, 46);
}
#input_user_id:focus {
    outline: none !important;
    border-color: rgb(22, 255, 94);
    box-shadow: 0 0 10px 5px rgb(22, 255, 94);
}
#join-buttom {
    width: 25%;
    max-width: 160px;
    min-width: 88px;
    height: 50px;
    margin-top: 10px;
    border: 2px solid rgb(22, 255, 94);
    background-color: transparent;
    color: rgb(22, 255, 94);
    font-family: "DungGeunMo";
    font-size: x-large;
}
#join-buttom:hover {
    background-color: rgb(22, 255, 94);
    color: #000;
}

.modal-wrapper {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: none;
    justify-content: center;
    align-items: center;
    font-family: "DungGeunMo";
    font-size: x-large;
}

.modal {
    background-color: rgb(172, 172, 172);
    width: 25vw;
    min-width: 250px;
    height: 20vw;
    border: 2px solid rgb(80, 80, 80);
    display: flex;
    flex-direction: column;
    justify-content: space-between;
}

.modal-title {
    background-image: linear-gradient(270deg, #1085d2 0%, #00007b 100%);
    box-sizing: border-box;
    width: 100%;
    border: 2px solid rgb(80, 80, 80);
    box-shadow: 1px 1px black, inset 2px 2px white;
    padding-left: 10px;
    color: white;
}

.modal-content {
    background-color: rgb(172, 172, 172);
    flex-basis: 80%;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    padding: 10px;
    font-size: large;
}
.close_modal {
    align-items: center;
    flex-basis: 20%;
    display: flex;
    justify-content: center;
}

.modal-close-button {
    border: 2px solid rgb(80, 80, 80);
    box-shadow: 1px 1px black, inset 2px 2px white;
    cursor: pointer;
}

#loginBar {
    box-sizing: border-box;
    background-image: linear-gradient(270deg, #a0d9ff 0%, #00007b 100%);
    border: 2px solid rgb(80, 80, 80);
    box-shadow: 1px 1px black, inset 2px 2px white;
    position: absolute;
    top: 0;
    color: white;
    width: 100%;
    height: 38px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    cursor: grab;
}
</style>
