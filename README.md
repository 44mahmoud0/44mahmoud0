// بيانات المستخدمين (محفوظة في LocalStorage)
let users = JSON.parse(localStorage.getItem('users')) || [];
let messages = JSON.parse(localStorage.getItem('messages')) || [];

// العناصر في الصفحة
const loginSection = document.getElementById('login-section');
const registerSection = document.getElementById('register-section');
const chatSection = document.getElementById('chat-section');
const currentUserSpan = document.getElementById('current-user');
const chatBox = document.getElementById('chat-box');
const messageInput = document.getElementById('message');

// المستخدم الحالي
let currentUser = localStorage.getItem('currentUser') || null;

// عرض قسم التسجيل
function showRegister() {
    loginSection.style.display = 'none';
    registerSection.style.display = 'block';
}

// عرض قسم تسجيل الدخول
function showLogin() {
    registerSection.style.display = 'none';
    loginSection.style.display = 'block';
}

// تسجيل الدخول
function login() {
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;

    const user = users.find(u => u.username === username && u.password === password);
    if (user) {
        localStorage.setItem('currentUser', username);
        currentUser = username;
        showChat();
    } else {
        alert('اسم المستخدم أو كلمة المرور غير صحيحة!');
    }
}

// إنشاء حساب
function register() {
    const newUsername = document.getElementById('new-username').value;
    const newPassword = document.getElementById('new-password').value;

    if (users.some(u => u.username === newUsername)) {
        alert('اسم المستخدم موجود مسبقًا!');
    } else {
        users.push({ username: newUsername, password: newPassword });
        localStorage.setItem('users', JSON.stringify(users));
        alert('تم إنشاء الحساب بنجاح!');
        showLogin();
    }
}

// عرض شاشة الدردشة بعد تسجيل الدخول
function showChat() {
    loginSection.style.display = 'none';
    registerSection.style.display = 'none';
    chatSection.style.display = 'block';
    currentUserSpan.textContent = currentUser;
    loadMessages();
}

// إرسال رسالة
function sendMessage() {
    const message = messageInput.value.trim();
    if (message !== '') {
        const timestamp = new Date().toLocaleTimeString();
        const messageData = { user: currentUser, text: message, time: timestamp };
        
        messages.push(messageData);
        localStorage.setItem('messages', JSON.stringify(messages));

        appendMessage(messageData);
        messageInput.value = '';
        chatBox.scrollTop = chatBox.scrollHeight;
    }
}

// تحميل الرسائل من `localStorage`
function loadMessages() {
    chatBox.innerHTML = ''; // مسح الرسائل القديمة
    messages.forEach(msg => appendMessage(msg));
}

// إضافة رسالة إلى واجهة المستخدم
function appendMessage(msg) {
    const messageElement = document.createElement('div');
    messageElement.classList.add('message');
    messageElement.innerHTML = `<strong>${msg.user}</strong> (${msg.time}): ${msg.text}`;
    chatBox.appendChild(messageElement);
}

// تسجيل الخروج
function logout() {
    localStorage.removeItem('currentUser');
    currentUser = null;
    chatSection.style.display = 'none';
    showLogin();
}

// فحص إذا كان هناك مستخدم مسجل مسبقًا
if (currentUser) {
    showChat();
}
