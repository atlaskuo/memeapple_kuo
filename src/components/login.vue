<template>
            <div v-if="showPopup" class="popup">
                    <div class="popup-content">
                        
                    <div class="join_login">
                        
                        <div class="join_loginHeader" v-if="currentlogin === 'login'">
                            <button class="btn btn_acc" @click="loginbtn" :class="{active: activeButton === 'login'}">會員登入</button>
                            <button class="btn btn_accj" @click="joinbtn" :class="{active: activeButton === 'join'}">會員註冊</button>
                        </div>
                        <div class="join_loginHeader" v-if="currentlogin === 'join'">
                            <button class="btn btn_joina" @click="loginbtn" :class="{active: activeButton === 'login'}">會員登入</button>
                            <button class="btn btn_join" @click="joinbtn" :class="{active: activeButton === 'join'}">會員註冊</button>
                        </div>
                    
                    <div class="mainLogin" v-if="currentlogin === 'login'">
                        <form @submit.prevent="submitLogin">
                            <span class="close" @click="closePopup">&times;</span>
                            <ol id="mainLogin">
                            <li><h3>會員登入</h3></li>
                            <li><i class="fa-solid fa-envelope"></i><h4>帳號</h4></li>
                            <li><input type="text" class="account" placeholder="請輸入帳號"  v-model="username"></li>
                            <li><i class="fa-solid fa-lock"></i><h4>密碼</h4></li>
                            <li><input type="password" class="PWD" placeholder="請輸入密碼" v-model="password"></li>
                            <li><button type="submit" class="btn btnlogin">登入</button></li> 
                            </ol> 
                            <div class="checkforget">
                                <div>
                                    <input type="checkbox" name="onlogin" id="onlogin" checked="checked">保持登入
                                </div>
                                <a href="#">忘記密碼？</a>
                            </div>
                            <div class="linef">
                                <span class="fline"></span>
                                <p class="flinetext">or</p>
                                <span class="fline"></span>
                            </div>
                            <div class="outloginbtn">
                            <button class="btn btnfb"><i class="fa-brands fa-square-facebook"></i></button>   
                            <!-- <button class="btn btngoogle"><i class="fa-brands fa-google"></i></button> -->
                            <button class="btn btngoogle"><img src="@/assets/img/icons_google.png"></button>
                            <button class="btn btnline"><i class="fa-brands fa-line"></i></button>    
                            </div>
                        </form>
                    </div>

                    <div class="mainLogin mainJoin" v-if="currentlogin === 'join'">
                        <form @submit.prevent="submitRegister">
                            <span class="close" @click="closePopup">&times;</span>
                            <ol id="mainJoin">
                            <li><h3>會員註冊</h3></li>
                            <li><i class="fa-solid fa-envelope"></i><h4>帳號</h4></li>
                            <li><input type="email" class="account" placeholder="請輸入信箱" v-model="registerEmail" required>
                                <span class="error">{{ registerErrors.email }}</span>
                            </li>
                            <li><i class="fa-solid fa-lock"></i><h4>建立密碼</h4>
                                <span class="error">{{ registerErrors.password }}</span>
                            </li>
                            <li><input type="text" class="joinPWD" placeholder="請輸入密碼" v-model="registerPassword"></li>
                            <li><i class="fa-solid fa-circle-check"></i><h4>請再次輸入密碼</h4>
                                <span class="error">{{ registerErrors.passwordConfirm }}</span>
                            </li>
                            <li><input type="text" class="joinPWD" placeholder="請再次輸入密碼" v-model="registerPasswordConfirm"></li>
                            <li><i class="fa-solid fa-user"></i><h4>姓名</h4></li>
                            <li><input type="text" class="joinname" placeholder="請輸入姓名" v-model="registerName"></li>
                            <li><i class="fa-solid fa-phone"></i><h4>電話號碼</h4></li>
                            <li><input type="text" class="joinphone" placeholder="請輸入電話" v-model="registerPhone" pattern="^09\d{8}$">
                                <span class="error">{{ registerErrors.phone }}</span>
                            </li>
                            <li class="checkservers"><input type="checkbox" class="checkserver" v-model="agreeTerms"><p>我同意</p><router-link to="/Privacy/" target="_blank">隱私權政策</router-link><p>&</p><router-link to="/Terms/" target="_blank">服務條款</router-link></li>
                            <li><button type="submit" class="btn btnlogin">註冊帳號</button></li> 
                            </ol> 
                        </form>
                        </div>
                    </div>
                 
                </div>
            </div>           

   
</template>

<script>
import '../assets/js/vue.global';
import '../assets/css/style.css';
import axios from 'axios';


export default {
 
    data (){
            return {
                currentlogin: 'login', // 切換登入/註冊狀態
                // currentStartIndex: 0,
                showPopup: true,
                username: '',
                password: '',
                registerEmail: '',
                registerPassword: '',
                registerPasswordConfirm: '',
                registerName: '',
                registerPhone: '',
                agreeTerms: false,
                activeButton: 'login',
                rememberMe: false,
                registerErrors: {
                    email: '',
                    password: '',
                    phone: '',
                    passwordConfirm: ''
                },
            };
    },
    created() {
        this.checkLoginStatus();
    },    
    methods: {
        loginbtn (){
            this.currentlogin = 'login';
            this.activeButton = 'login';
            // this.currentStartIndex = 0;
        },
        joinbtn(){
            this.currentlogin = 'join';
            this.activeButton = 'join';
            // this.currentStartIndex = 0;
        },
        closePopup() {
            this.showPopup = false;
            this.$emit('close'); // 可選：通知父組件關閉
        },
        async submitLogin() {
            try {
                const response = await axios.post('http://localhost/sweethome/meme/public/php/api/Login.php', {
                    username: this.username,
                    password: this.password
                });
                if (response.data.status === "success") {
                    alert("登入成功");
                    // 進行登入後的操作，例如重定向
                    // 如果選擇了保持登入，使用 localStorage
                    if (this.rememberMe) {
                        localStorage.setItem('user', JSON.stringify({ username: this.username }));
                    } else {
                        // 否則使用 sessionStorage
                        sessionStorage.setItem('user', JSON.stringify({ username: this.username }));
                    }
                    this.$emit('login', response.data.user); // 發出登入事件，並傳遞用戶資料
                    this.closePopup(); // 關閉彈窗

                } else {
                    alert(response.data.message); // 顯示錯誤消息
                }
            } catch (error) {
                console.error(error);
                alert("發生錯誤，請稍後再試。");
            }
        },
        validateEmail(email) {
        const emailPattern = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
        return emailPattern.test(email);
        },
        validatePassword(password) {
            const regex = /^(?=.*[A-Za-z])(?=.*\d).{6,}$/; // 至少6個字符，包含字母和數字
            return regex.test(password);
        },
        validatePhone(phone) {
            const regex = /^09\d{8}$/; // 以09開頭的10位數字
            return regex.test(phone);
        },
        async submitRegister() {
            // 清空錯誤信息
        let errorMessages = [];

        // 驗證輸入格式
        if (!this.validateEmail(this.registerEmail)) {
            errorMessages.push('請輸入有效的電子郵件地址');
        }
        if (!this.validatePassword(this.registerPassword)) {
            errorMessages.push('密碼必須至少6個字符，並包含字母和數字');
        }
        if (this.registerPassword !== this.registerPasswordConfirm) {
            errorMessages.push('兩次密碼輸入不一致');
        }
        if (!this.validatePhone(this.registerPhone)) {
            errorMessages.push('手機號碼必須以09開頭，並包含10位數字');
        }
        if (!this.agreeTerms) {
            errorMessages.push('請同意隱私權政策和服務條款');
        }

        // 如果有錯誤，則顯示所有錯誤信息
        if (errorMessages.length > 0) {
            alert(errorMessages.join('\n'));
            return; // 退出方法，不進行註冊請求
        }

            // 如果所有檢查都通過，則進行註冊請求
                try {
                    const response = await axios.post('http://localhost/sweethome/meme/public/php/api/Register.php', {
                        email: this.registerEmail,
                        password: this.registerPassword,
                        name: this.registerName,
                        phone: this.registerPhone
                    });
                     
                    if (response.data.status === "success") {
                        alert("註冊成功");
                        // 可以在這裡進行重定向或其他操作
                        

                        // 清空註冊資料
                        this.registerEmail = '';
                        this.registerPassword = '';
                        this.registerPasswordConfirm = '';
                        this.registerName = '';
                        this.registerPhone = '';
                        this.agreeTerms = false;

                        this.loginbtn(); // 切換回登入頁
                    } else {
                        alert("發生錯誤：" + response.data.message); // 顯示錯誤消息
                    }
                } catch (error) {
                    console.error(error);
                    alert("發生錯誤，請稍後再試。");
                }
        },
        checkLoginStatus() {
            const user = sessionStorage.getItem('user') || localStorage.getItem('user');
            if (user) {
                const parsedUser = JSON.parse(user);
                this.username = parsedUser.username;
                // 根據需要設置其他狀態，例如自動登入或更新界面
            }
        },
        logout() {
            localStorage.removeItem('user'); // 移除 localStorage 中的用戶信息
            sessionStorage.removeItem('user'); // 移除 sessionStorage 中的用戶信息
            // 進行登出後的操作，例如重定向
        }    
    },
}
</script>

