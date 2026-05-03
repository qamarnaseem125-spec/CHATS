<!DOCTYPE html>
<html>
<head>
    <title>Insta Code Chat</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="theme-color" content="#0095f6">
    <link rel="manifest" href="manifest.json">
    <link rel="icon" href="icon.svg" type="image/svg+xml">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Insta Code Chat">
    <style>
        body { font-family: sans-serif; margin: 0; background: #fafafa; display: flex; justify-content: center; min-height: 100vh; }
        .container { background: white; border: 1px solid #dbdbdb; width: 100%; max-width: 400px; display: flex; flex-direction: column; min-height: 100vh; }
        header { padding: 15px; border-bottom: 1px solid #dbdbdb; text-align: center; font-weight: bold; font-style: italic; font-size: 22px; display: flex; flex-direction: column; align-items: center; }
        .tabs { display: flex; gap: 10px; margin-top: 10px; }
        .tab-btn { padding: 8px 16px; background: #f0f0f0; border: none; border-radius: 20px; cursor: pointer; font-size: 14px; }
        .tab-btn.active { background: #0095f6; color: white; }
        .profile-view { padding: 20px; display: none; text-align: center; }
        .profile-pic { width: 100px; height: 100px; border-radius: 50%; object-fit: cover; margin: 10px auto; display: block; }
        .profile-input { width: 100%; padding: 10px; margin: 10px 0; border: 1px solid #dbdbdb; border-radius: 5px; }
        .save-btn { padding: 10px 20px; background: #0095f6; color: white; border: none; border-radius: 5px; cursor: pointer; }
        .chat-selector { padding: 10px; border-bottom: 1px solid #dbdbdb; display: flex; align-items: center; gap: 10px; background: #f8f8f8; }
        .tweet-feed { padding: 10px; display: none; }
        .tweet-box { padding: 12px; border: 1px solid #dbdbdb; border-radius: 12px; margin: 10px 0; background: #fafafa; }
        .tweet-box textarea { width: 100%; min-height: 80px; resize: vertical; border: 1px solid #dbdbdb; border-radius: 10px; padding: 10px; }
        .tweet-btn { margin-top: 8px; padding: 10px 18px; background: #1da1f2; color: white; border: none; border-radius: 999px; cursor: pointer; }
        .tweet-list { display: flex; flex-direction: column; gap: 12px; margin-top: 10px; }
        .tweet-item { padding: 14px; border: 1px solid #eee; border-radius: 16px; background: white; }
        .tweet-user { font-weight: bold; margin-bottom: 6px; display: flex; align-items: center; gap: 8px; }
        .tweet-time { font-size: 11px; color: #777; }
        .tweet-text { white-space: pre-wrap; font-size: 14px; margin-top: 8px; }
        .tweet-actions { margin-top: 10px; display: flex; gap: 8px; flex-wrap: wrap; }
        .tweet-action-btn { border: 1px solid #dbdbdb; border-radius: 999px; padding: 6px 10px; background: #fff; color: #333; cursor: pointer; font-size: 12px; }
        .tweet-action-btn.active { background: #1da1f2; color: white; border-color: #1da1f2; }
        .tweet-count { font-size: 12px; color: #555; margin-left: 4px; }
        .tweet-card-image { width: 100%; border-radius: 12px; margin-top: 10px; }
        .chat-selector select { padding: 5px; border: 1px solid #dbdbdb; border-radius: 4px; }
        .users-list { padding: 10px; display: none; }
        .user-item { display: flex; justify-content: space-between; align-items: center; padding: 10px; border-bottom: 1px solid #eee; }
        .follow-btn { padding: 5px 10px; background: #0095f6; color: white; border: none; border-radius: 4px; cursor: pointer; }
        .follow-btn.followed { background: #666; }
        .online-status { font-size: 12px; color: #0095f6; }
        #login-screen { padding: 40px 20px; text-align: center; }
        .inp { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #dbdbdb; border-radius: 5px; box-sizing: border-box; background: #fafafa; }
        .btn { width: 100%; padding: 12px; background: #0095f6; color: white; border: none; border-radius: 5px; font-weight: bold; cursor: pointer; margin-top: 10px; }
        #chat-screen { display: none; flex: 1; flex-direction: column; height: 100%; overflow: hidden; }
        #messages { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 8px; }
        .msg { padding: 8px 12px; border-radius: 15px; max-width: 75%; font-size: 14px; position: relative; }
        .sent { align-self: flex-end; background: #0095f6; color: white; }
        .recv { align-self: flex-start; background: #efefef; color: black; }
        .s-name { font-size: 10px; font-weight: bold; display: block; margin-bottom: 2px; opacity: 0.7; }
        .timestamp { font-size: 10px; opacity: 0.6; margin-top: 2px; }
        .status { font-size: 10px; opacity: 0.6; margin-top: 2px; }
        .reaction-bar { display: flex; flex-wrap: wrap; gap: 6px; margin-top: 8px; }
        .reaction-pill { display: inline-flex; align-items: center; gap: 4px; padding: 4px 8px; border-radius: 999px; background: rgba(255,255,255,0.18); font-size: 12px; cursor: pointer; }
        .reaction-pill.me { box-shadow: inset 0 0 0 1px rgba(0,0,0,0.08); }
        .react-button { margin-top: 6px; font-size: 12px; color: #555; background: none; border: none; cursor: pointer; opacity: 0.8; }
        .react-button:hover { opacity: 1; }
        #install-banner { display: none; background: #0095f6; color: white; padding: 10px 15px; text-align: center; border-radius: 12px; margin: 10px; }
        #install-banner button { margin-left: 10px; background: white; color: #0095f6; border: none; padding: 8px 14px; border-radius: 999px; font-weight: bold; cursor: pointer; }
        .typing { font-size: 12px; color: #999; padding: 5px; }
        .input-box { padding: 15px; border-top: 1px solid #dbdbdb; display: flex; gap: 10px; align-items: center; }
        .emoji-btn { background: none; border: none; font-size: 20px; cursor: pointer; }
        .file-input { display: none; }
        .file-btn { background: none; border: none; font-size: 18px; cursor: pointer; }
        img { max-width: 100%; border-radius: 10px; }
    </style>
</head>
<body>
    <div class="container">
        <header>
            Insta Code Chat
            <div class="tabs">
                <button class="tab-btn active" onclick="showChats()">Chats</button>
                <button class="tab-btn" onclick="showFeed()">Feed</button>
                <button class="tab-btn" onclick="showUsers()">Users</button>
                <button class="tab-btn" onclick="showProfile()">Profile</button>
            </div>
        </header>

        <div id="install-banner">
            Install app for faster access
            <button id="install-button" onclick="installApp()">Install</button>
        </div>

        <div id="login-screen">
            <p style="color: #8e8e8e;">Enter Name & 4-Digit Secret Code</p>
            <input type="text" id="user-name" class="inp" placeholder="Username">
            <input type="password" id="user-code" class="inp" placeholder="4-Digit Secret Code">
            <button onclick="accessChat()" class="btn">Enter Chat</button>
            <p id="status" style="font-size: 12px; margin-top: 10px; color: #999;"></p>
        </div>

        <div id="chat-screen">
            <div class="chat-selector">
                <label>Chat:</label>
                <select id="chat-select" onchange="switchChat()">
                    <option value="group">Group</option>
                </select>
                <div style="display:flex; gap:8px; align-items:center; margin-left:auto;">
                    <input id="new-chat-name" type="text" class="inp" placeholder="New chat name" style="flex:1; margin:0;" />
                    <button onclick="createChat()" class="btn" style="padding:8px 12px; margin:0;">Create</button>
                </div>
            </div>
            <div id="chat-link-container" style="display:none; padding:10px 0; font-size:12px;">
                Link: <a id="chat-link-url" href="#" target="_blank"></a>
                <button onclick="copyChatLink()" style="background:none; border:none; color:#0095f6; cursor:pointer; font-size:12px; margin-left:8px;">Copy</button>
            </div>
            <div class="users-list" id="users-list">
                <!-- Users will be populated here -->
            </div>
            <div class="tweet-feed" id="tweet-feed">
                <div class="tweet-box">
                    <textarea id="tweet-text" placeholder="What's happening?" maxlength="280"></textarea>
                    <button class="tweet-btn" onclick="sendTweet()">Tweet</button>
                </div>
                <div class="tweet-list" id="tweet-list"></div>
            </div>
            <div class="profile-view" id="profile-view">
                <button id="profile-back-btn" onclick="showUsers()" style="display:none; margin-bottom:10px;">Back</button>
                <h3 id="profile-username"></h3>
                <img id="profile-pic" src="https://via.placeholder.com/100" alt="Profile Pic" class="profile-pic">
                <input type="file" id="pic-input" accept="image/*" onchange="previewPic()" style="display: none;">
                <button id="change-pic-btn" onclick="document.getElementById('pic-input').click()" style="display:none; margin:10px;">Change Pic</button>
                <input type="text" id="bio-input" class="profile-input" placeholder="Bio" readonly>
                <button onclick="saveProfile()" class="save-btn" style="display: none;">Save Profile</button>
            </div>
            <div id="messages"></div>
            <div id="typing-indicator" class="typing" style="display: none;"></div>
            <div class="input-box">
                <input type="text" id="m-text" placeholder="Message..." style="flex:1; border:none; outline:none;" onkeypress="if(event.key==='Enter') send()">
                <button class="emoji-btn" onclick="toggleEmoji()">😊</button>
                <button class="file-btn" onclick="toggleStickerPicker()">🖼️</button>
                <button class="file-btn" onclick="toggleSongPicker()">🎵</button>
                <button class="file-btn" onclick="sendSongUrl()">🎧</button>
                <button class="file-btn" onclick="document.getElementById('audio-input').click()">🎤</button>
                <input type="file" id="file-input" class="file-input" accept="image/*" onchange="sendImage()">
                <input type="file" id="audio-input" class="file-input" accept="audio/*" onchange="sendAudio()">
                <button onclick="send()" style="background:none; color:#0095f6; width:auto; padding:0; font-weight:bold;">Send</button>
            </div>
            <button onclick="location.reload()" style="background:none; color:grey; border:none; padding:5px; font-size:10px;">Switch User</button>
        </div>
    </div>

    <div id="emoji-picker" style="display: none; position: absolute; bottom: 60px; right: 20px; background: white; border: 1px solid #dbdbdb; padding: 10px; border-radius: 5px;">
        😀 😃 😂 😍 👍 ❤️ 🔥 👏
    </div>
    <div id="sticker-picker" style="display: none; position: absolute; bottom: 60px; right: 20px; background: white; border: 1px solid #dbdbdb; padding: 10px; border-radius: 5px; width: 240px;">
        <div style="font-size: 12px; margin-bottom: 8px; font-weight: bold;">Tap sticker to send</div>
        <div style="display:flex; flex-wrap:wrap; gap:8px; justify-content:center;">
            <img src="https://img.icons8.com/emoji/96/000000/red-heart.png" style="width:64px;cursor:pointer;" onclick="sendSticker('https://img.icons8.com/emoji/96/000000/red-heart.png')">
            <img src="https://img.icons8.com/emoji/96/000000/party-popper.png" style="width:64px;cursor:pointer;" onclick="sendSticker('https://img.icons8.com/emoji/96/000000/party-popper.png')">
            <img src="https://img.icons8.com/emoji/96/000000/face-with-tears-of-joy.png" style="width:64px;cursor:pointer;" onclick="sendSticker('https://img.icons8.com/emoji/96/000000/face-with-tears-of-joy.png')">
            <img src="https://img.icons8.com/emoji/96/000000/fire.png" style="width:64px;cursor:pointer;" onclick="sendSticker('https://img.icons8.com/emoji/96/000000/fire.png')">
            <img src="https://img.icons8.com/emoji/96/000000/thumbs-up.png" style="width:64px;cursor:pointer;" onclick="sendSticker('https://img.icons8.com/emoji/96/000000/thumbs-up.png')">
            <img src="https://img.icons8.com/emoji/96/000000/crying-face.png" style="width:64px;cursor:pointer;" onclick="sendSticker('https://img.icons8.com/emoji/96/000000/crying-face.png')">
        </div>
    </div>
    <div id="song-picker" style="display: none; position: absolute; bottom: 60px; right: 20px; background: white; border: 1px solid #dbdbdb; padding: 10px; border-radius: 5px; width: 260px; max-height: 260px; overflow:auto;">
        <div style="font-size: 12px; margin-bottom: 8px; font-weight: bold;">Choose song to send</div>
        <div style="display:flex; flex-direction:column; gap:10px;">
            <div style="border:1px solid #eee; border-radius:8px; padding:8px; background:#fafafa; cursor:pointer;" onclick="sendSong('https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3','Vibe Track','Insta Beats')">
                <div style="font-weight:bold;">Vibe Track</div>
                <div style="font-size:11px; color:#666;">Insta Beats</div>
            </div>
            <div style="border:1px solid #eee; border-radius:8px; padding:8px; background:#fafafa; cursor:pointer;" onclick="sendSong('https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3','Chill Wave','Cloud Music')">
                <div style="font-weight:bold;">Chill Wave</div>
                <div style="font-size:11px; color:#666;">Cloud Music</div>
            </div>
            <div style="border:1px solid #eee; border-radius:8px; padding:8px; background:#fafafa; cursor:pointer;" onclick="sendSong('https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3','Happy Groove','Demo Band')">
                <div style="font-weight:bold;">Happy Groove</div>
                <div style="font-size:11px; color:#666;">Demo Band</div>
            </div>
        </div>
    </div>
    <div id="reaction-picker" style="display: none; position: absolute; bottom: 60px; right: 20px; background: white; border: 1px solid #dbdbdb; padding: 10px; border-radius: 5px; width: 220px;">
        <div style="font-size: 12px; margin-bottom: 8px; font-weight: bold;">React with</div>
        <div style="display:flex; gap:10px; flex-wrap:wrap; justify-content:center;">
            <button style="font-size: 18px; background:none; border:none; cursor:pointer;" onclick="sendReaction('❤️')">❤️</button>
            <button style="font-size: 18px; background:none; border:none; cursor:pointer;" onclick="sendReaction('😂')">😂</button>
            <button style="font-size: 18px; background:none; border:none; cursor:pointer;" onclick="sendReaction('🔥')">🔥</button>
            <button style="font-size: 18px; background:none; border:none; cursor:pointer;" onclick="sendReaction('👍')">👍</button>
            <button style="font-size: 18px; background:none; border:none; cursor:pointer;" onclick="sendReaction('😮')">😮</button>
            <button style="font-size: 18px; background:none; border:none; cursor:pointer;" onclick="sendReaction('🎉')">🎉</button>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getFirestore, collection, doc, setDoc, getDoc, addDoc, query, orderBy, onSnapshot, updateDoc, serverTimestamp, arrayUnion, arrayRemove, where, getDocs, deleteDoc } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";
        import { getStorage, ref, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";

        const firebaseConfig = {
            apiKey: "AIzaSyDYtlTOJNfNczpeQY2XiqE6ehimc8ISfEU",
            authDomain: "group-chats-arham.firebaseapp.com",
            projectId: "group-chats-arham",
            storageBucket: "group-chats-arham.firebasestorage.app",
            messagingSenderId: "79942638812",
            appId: "1:79942638812:web:1603cb872ff9613bff2819"
        };

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const storage = getStorage(app);
        let CURRENT_USER = "";
        let typingTimeout;
        let reactionTargetId = null;
        let currentChatId = "group";
        let messagesUnsubscribe = null;
        let follows = new Set();
        let profileData = { bio: "", picUrl: "https://via.placeholder.com/100" };
        let deferredInstallPrompt = null;

        window.addEventListener('beforeinstallprompt', (e) => {
            e.preventDefault();
            deferredInstallPrompt = e;
            const banner = document.getElementById('install-banner');
            if (banner) banner.style.display = 'block';
        });

        window.addEventListener('appinstalled', () => {
            const banner = document.getElementById('install-banner');
            if (banner) banner.style.display = 'none';
            console.log('App installed');
        });

        window.installApp = async () => {
            const banner = document.getElementById('install-banner');
            if (!deferredInstallPrompt) {
                alert('Install prompt not available yet. Open the app on a supported browser or reload.');
                return;
            }
            deferredInstallPrompt.prompt();
            const choiceResult = await deferredInstallPrompt.userChoice;
            if (choiceResult.outcome === 'accepted') {
                console.log('User accepted the install prompt');
                if (banner) banner.style.display = 'none';
            } else {
                console.log('User dismissed the install prompt');
            }
            deferredInstallPrompt = null;
        };

        window.accessChat = async () => {
            const name = document.getElementById('user-name').value.trim().toLowerCase();
            const code = document.getElementById('user-code').value.trim();
            const status = document.getElementById('status');
            status.innerText = "";

            if (!name || !code) {
                status.innerText = "Fill both fields!";
                status.style.color = "red";
                return;
            }
            if (code.length !== 4 || isNaN(code)) {
                status.innerText = "Code must be exactly 4 digits!";
                status.style.color = "red";
                return;
            }

            try {
                const userRef = doc(db, "insta_users", name);
                const userSnap = await getDoc(userRef);

                if (userSnap.exists()) {
                    if (userSnap.data().code === code) {
                        await setDoc(doc(db, "presence", name), { online: true, lastSeen: serverTimestamp() });
                        startChat(name);
                        status.innerText = "Login successful!";
                        status.style.color = "green";
                    } else {
                        status.innerText = "Wrong Code for this name!";
                        status.style.color = "red";
                    }
                } else {
                    await setDoc(userRef, { name: name, code: code });
                    await setDoc(doc(db, "presence", name), { online: true, lastSeen: serverTimestamp() });
                    startChat(name);
                    status.innerText = "New user created and logged in!";
                    status.style.color = "green";
                }
            } catch (error) {
                console.error("Login error:", error);
                status.innerText = "Error accessing chat. Check console.";
                status.style.color = "red";
            }
        };

        async function startChat(name) {
            CURRENT_USER = name;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('chat-screen').style.display = 'flex';
            
            // Profile listener
            onSnapshot(doc(db, "profiles", CURRENT_USER), (snap) => {
                if (snap.exists()) {
                    profileData = snap.data();
                    document.getElementById('profile-pic').src = profileData.picUrl || "https://via.placeholder.com/100";
                    document.getElementById('bio-input').value = profileData.bio || "";
                }
            });

            const select = document.getElementById('chat-select');
            const params = new URLSearchParams(window.location.search);
            const requestedChat = params.get('chat');
            if (requestedChat && requestedChat !== 'group') {
                currentChatId = requestedChat;
                let optionExists = Array.from(select.options).some(opt => opt.value === requestedChat);
                if (!optionExists) {
                    const chatSnap = await getDoc(doc(db, "chats", requestedChat));
                    const chatName = chatSnap.exists() ? chatSnap.data().name : requestedChat;
                    const option = document.createElement('option');
                    option.value = requestedChat;
                    option.textContent = `Chat: ${chatName}`;
                    select.appendChild(option);
                }
                select.value = requestedChat;
                document.getElementById('chat-link-container').style.display = 'block';
                document.getElementById('chat-link-url').href = `${window.location.origin}${window.location.pathname}?chat=${encodeURIComponent(requestedChat)}`;
                document.getElementById('chat-link-url').textContent = `${window.location.origin}${window.location.pathname}?chat=${encodeURIComponent(requestedChat)}`;
            }
            listenMessages(currentChatId);

            onSnapshot(collection(db, "presence"), (snap) => {
                let count = 0;
                const select = document.getElementById('chat-select');
                const usersList = document.getElementById('users-list');
                usersList.innerHTML = "";
                const preserved = Array.from(select.options).filter(o => o.value !== 'group');
                while (select.options.length > 1) select.remove(1);
                preserved.forEach(o => select.appendChild(o));
                snap.forEach(d => {
                    if (d.data().online) {
                        count++;
                        if (d.id !== CURRENT_USER) {
                            // Add to users list
                            const userDiv = document.createElement('div');
                            userDiv.className = 'user-item';
                            userDiv.innerHTML = `<span>${d.id}</span>`;
                            const viewBtn = document.createElement('button');
                            viewBtn.className = 'follow-btn';
                            viewBtn.textContent = 'View Profile';
                            viewBtn.onclick = () => viewProfile(d.id);
                            userDiv.appendChild(viewBtn);
                            const followBtn = document.createElement('button');
                            followBtn.className = 'follow-btn';
                            followBtn.textContent = follows.has(d.id) ? 'Unfollow' : 'Follow';
                            followBtn.classList.toggle('followed', follows.has(d.id));
                            followBtn.onclick = () => toggleFollow(d.id);
                            userDiv.appendChild(followBtn);
                            usersList.appendChild(userDiv);

                            // Add to DM select if followed
                            if (follows.has(d.id)) {
                                const value = `dm-${[CURRENT_USER, d.id].sort().join('-')}`;
                                if (!Array.from(select.options).some(o => o.value === value)) {
                                    const option = document.createElement('option');
                                    option.value = value;
                                    option.textContent = `DM: ${d.id}`;
                                    select.appendChild(option);
                                }
                            }
                        }
                    }
                });
                document.getElementById('online-count').innerText = `${count} online`;
            });

            const tweetQuery = query(collection(db, "tweets"), orderBy("time", "desc"));
            onSnapshot(tweetQuery, (snap) => {
                const list = document.getElementById('tweet-list');
                list.innerHTML = '';
                snap.forEach(docSnap => {
                    const t = docSnap.data();
                    const item = document.createElement('div');
                    item.className = 'tweet-item';
                    const header = document.createElement('div');
                    header.className = 'tweet-user';
                    header.innerHTML = `<span>${t.author}</span><span class="tweet-time">${new Date(t.time?.toDate()).toLocaleString()}</span>`;
                    item.appendChild(header);
                    const text = document.createElement('div');
                    text.className = 'tweet-text';
                    text.textContent = t.text;
                    item.appendChild(text);
                    if (t.imageUrl) {
                        const img = document.createElement('img');
                        img.src = t.imageUrl;
                        img.className = 'tweet-card-image';
                        item.appendChild(img);
                    }
                    const actions = document.createElement('div');
                    actions.className = 'tweet-actions';
                    const likeBtn = document.createElement('button');
                    likeBtn.className = 'tweet-action-btn' + (t.likes?.includes(CURRENT_USER) ? ' active' : '');
                    likeBtn.textContent = 'Like';
                    likeBtn.onclick = () => toggleTweetLike(docSnap.id, t.likes || []);
                    actions.appendChild(likeBtn);
                    const count = document.createElement('span');
                    count.className = 'tweet-count';
                    count.textContent = `${(t.likes || []).length}`;
                    actions.appendChild(count);
                    item.appendChild(actions);
                    list.appendChild(item);
                });
            });

            listenMessages(currentChatId);

            onSnapshot(collection(db, "typing"), (snap) => {
                let typingUsers = [];
                snap.forEach(d => {
                    if (d.data().isTyping && d.id !== CURRENT_USER) typingUsers.push(d.id);
                });
                const indicator = document.getElementById('typing-indicator');
                if (typingUsers.length > 0) {
                    indicator.innerText = `${typingUsers.join(', ')} is typing...`;
                    indicator.style.display = 'block';
                } else {
                    indicator.style.display = 'none';
                }
            });

            document.getElementById('m-text').addEventListener('input', () => {
                setDoc(doc(db, "typing", CURRENT_USER), { isTyping: true });
                clearTimeout(typingTimeout);
                typingTimeout = setTimeout(() => {
                    setDoc(doc(db, "typing", CURRENT_USER), { isTyping: false });
                }, 1000);
            });
        }

        window.send = async () => {
            const inp = document.getElementById('m-text');
            const text = inp.value.trim();
            if (!text) return;

            try {
                await addDoc(collection(db, "insta_messages"), {
                    text: text,
                    sender: CURRENT_USER,
                    time: serverTimestamp(),
                    type: 'text',
                    seen: false,
                    seenBy: [],
                    chatId: currentChatId
                });
                inp.value = "";
                setDoc(doc(db, "typing", CURRENT_USER), { isTyping: false });
            } catch (error) {
                console.error("Send error:", error);
                alert("Error sending message.");
            }
        };

        window.sendImage = async () => {
            const file = document.getElementById('file-input').files[0];
            if (!file) return;

            try {
                const storageRef = ref(storage, `images/${Date.now()}_${file.name}`);
                await uploadBytes(storageRef, file);
                const url = await getDownloadURL(storageRef);
                await addDoc(collection(db, "insta_messages"), {
                    url: url,
                    sender: CURRENT_USER,
                    time: serverTimestamp(),
                    type: 'image',
                    seen: false,
                    seenBy: [],
                    chatId: currentChatId
                });
                document.getElementById('file-input').value = "";
            } catch (error) {
                console.error("Upload error:", error);
                alert("Error uploading image.");
            }
        };

        window.sendAudio = async () => {
            const file = document.getElementById('audio-input').files[0];
            if (!file) return;

            try {
                const storageRef = ref(storage, `audio/${Date.now()}_${file.name}`);
                await uploadBytes(storageRef, file);
                const url = await getDownloadURL(storageRef);
                await addDoc(collection(db, "insta_messages"), {
                    audioUrl: url,
                    audioName: file.name,
                    sender: CURRENT_USER,
                    time: serverTimestamp(),
                    type: 'audio',
                    seen: false,
                    seenBy: [],
                    chatId: currentChatId
                });
                document.getElementById('audio-input').value = "";
            } catch (error) {
                console.error("Audio upload error:", error);
                alert("Error uploading audio.");
            }
        };

        window.sendSticker = async (stickerUrl) => {
            try {
                await addDoc(collection(db, "insta_messages"), {
                    stickerUrl: stickerUrl,
                    sender: CURRENT_USER,
                    time: serverTimestamp(),
                    type: 'sticker',
                    seen: false,
                    seenBy: [],
                    chatId: currentChatId
                });
                document.getElementById('sticker-picker').style.display = 'none';
            } catch (error) {
                console.error("Sticker send error:", error);
                alert("Error sending sticker.");
            }
        };

        window.sendSong = async (songUrl, songTitle, songArtist) => {
            try {
                await addDoc(collection(db, "insta_messages"), {
                    songUrl: songUrl,
                    songTitle: songTitle,
                    songArtist: songArtist,
                    sender: CURRENT_USER,
                    time: serverTimestamp(),
                    type: 'song',
                    seen: false,
                    seenBy: [],
                    chatId: currentChatId
                });
                document.getElementById('song-picker').style.display = 'none';
            } catch (error) {
                console.error("Song send error:", error);
                alert("Error sending song.");
            }
        };

        window.sendSongUrl = async () => {
            const url = prompt("Paste YouTube or MP3 URL to share:");
            if (!url) return;
            const trimmedUrl = url.trim();
            if (!trimmedUrl) return;

            const title = prompt("Song title (optional):")?.trim() || "Shared song";
            const artist = prompt("Artist / source (optional):")?.trim() || "";
            const isYoutube = /(?:youtube\.com\/watch\?v=|youtu\.be\/)/i.test(trimmedUrl);

            try {
                await addDoc(collection(db, "insta_messages"), {
                    songUrl: trimmedUrl,
                    songTitle: title,
                    songArtist: artist,
                    sender: CURRENT_USER,
                    time: serverTimestamp(),
                    type: isYoutube ? 'youtube' : 'songLink',
                    seen: false,
                    seenBy: [],
                    chatId: currentChatId
                });
            } catch (error) {
                console.error("Song URL send error:", error);
                alert("Error sharing the song link.");
            }
        };

        window.sendReaction = async (emoji) => {
            if (!reactionTargetId) return;
            try {
                const msgRef = doc(db, "insta_messages", reactionTargetId);
                const msgSnap = await getDoc(msgRef);
                if (!msgSnap.exists()) return;
                const msgData = msgSnap.data();
                const existing = msgData?.reactions?.[emoji] || [];
                const hasReacted = existing.includes(CURRENT_USER);
                if (hasReacted) {
                    await updateDoc(msgRef, { [`reactions.${emoji}`]: arrayRemove(CURRENT_USER) });
                } else {
                    await updateDoc(msgRef, { [`reactions.${emoji}`]: arrayUnion(CURRENT_USER) });
                }
            } catch (error) {
                console.error('Reaction send error:', error);
                alert('Could not update reaction.');
            } finally {
                reactionTargetId = null;
                document.getElementById('reaction-picker').style.display = 'none';
            }
        };

        function listenMessages(chatId) {
            if (messagesUnsubscribe) messagesUnsubscribe();
            const q = query(collection(db, "insta_messages"), where('chatId', '==', chatId), orderBy("time", "asc"));
            messagesUnsubscribe = onSnapshot(q, (snap) => {
                const box = document.getElementById('messages');
                box.innerHTML = "";
                snap.forEach(d => {
                    const m = d.data();
                    const isMe = m.sender === CURRENT_USER;
                    
                    const msgDiv = document.createElement('div');
                    msgDiv.className = `msg ${isMe ? 'sent' : 'recv'}`;
                    
                    const senderSpan = document.createElement('span');
                    senderSpan.className = 's-name';
                    senderSpan.textContent = m.sender;
                    msgDiv.appendChild(senderSpan);
                    
                    if (m.type === 'image') {
                        const img = document.createElement('img');
                        img.src = m.url;
                        msgDiv.appendChild(img);
                    } else if (m.type === 'sticker') {
                        const img = document.createElement('img');
                        img.src = m.stickerUrl;
                        img.style.width = '120px';
                        img.style.height = 'auto';
                        msgDiv.appendChild(img);
                    } else if (m.type === 'song') {
                        const songTitle = document.createElement('div');
                        songTitle.style.fontWeight = 'bold';
                        songTitle.style.marginBottom = '6px';
                        songTitle.textContent = `${m.songTitle} — ${m.songArtist}`;
                        msgDiv.appendChild(songTitle);

                        const audio = document.createElement('audio');
                        audio.src = m.songUrl;
                        audio.controls = true;
                        audio.style.width = '100%';
                        msgDiv.appendChild(audio);
                    } else if (m.type === 'youtube') {
                        const songTitle = document.createElement('div');
                        songTitle.style.fontWeight = 'bold';
                        songTitle.style.marginBottom = '6px';
                        songTitle.textContent = `${m.songTitle} — ${m.songArtist}`;
                        msgDiv.appendChild(songTitle);

                        const youtubeIdMatch = m.songUrl.match(/(?:youtube\.com\/watch\?v=|youtu\.be\/)([\w-]{11})/i);
                        if (youtubeIdMatch) {
                            const iframe = document.createElement('iframe');
                            iframe.width = '100%';
                            iframe.height = '180';
                            iframe.src = `https://www.youtube.com/embed/${youtubeIdMatch[1]}`;
                            iframe.allow = 'accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture';
                            iframe.allowFullscreen = true;
                            msgDiv.appendChild(iframe);
                        } else {
                            const link = document.createElement('a');
                            link.href = m.songUrl;
                            link.target = '_blank';
                            link.rel = 'noopener noreferrer';
                            link.textContent = 'Open song';
                            msgDiv.appendChild(link);
                        }
                    } else if (m.type === 'songLink') {
                        const songTitle = document.createElement('div');
                        songTitle.style.fontWeight = 'bold';
                        songTitle.style.marginBottom = '6px';
                        songTitle.textContent = `${m.songTitle} — ${m.songArtist}`;
                        msgDiv.appendChild(songTitle);

                        const audio = document.createElement('audio');
                        audio.src = m.songUrl;
                        audio.controls = true;
                        audio.style.width = '100%';
                        audio.addEventListener('error', () => {
                            const fallback = document.createElement('a');
                            fallback.href = m.songUrl;
                            fallback.target = '_blank';
                            fallback.rel = 'noopener noreferrer';
                            fallback.textContent = 'Open audio link';
                            msgDiv.replaceChild(fallback, audio);
                        });
                        msgDiv.appendChild(audio);
                    } else if (m.type === 'audio') {
                        const audio = document.createElement('audio');
                        audio.src = m.audioUrl;
                        audio.controls = true;
                        audio.style.width = '100%';
                        msgDiv.appendChild(audio);
                    } else {
                        msgDiv.appendChild(document.createTextNode(m.text));
                    }
                    
                    const timeSpan = document.createElement('div');
                    timeSpan.className = 'timestamp';
                    timeSpan.textContent = new Date(m.time?.toDate()).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
                    msgDiv.appendChild(timeSpan);
                    
                    if (isMe) {
                        const statusSpan = document.createElement('div');
                        statusSpan.className = 'status';
                        statusSpan.textContent = m.seen ? 'Seen' : 'Delivered';
                        msgDiv.appendChild(statusSpan);
                    }

                    const reactionBar = document.createElement('div');
                    reactionBar.className = 'reaction-bar';
                    if (m.reactions) {
                        Object.entries(m.reactions).forEach(([emoji, users]) => {
                            if (!users || users.length === 0) return;
                            const pill = document.createElement('div');
                            pill.className = 'reaction-pill' + (users.includes(CURRENT_USER) ? ' me' : '');
                            pill.textContent = `${emoji} ${users.length}`;
                            reactionBar.appendChild(pill);
                        });
                    }
                    const reactButton = document.createElement('button');
                    reactButton.className = 'react-button';
                    reactButton.textContent = 'React';
                    reactButton.onclick = () => openReactionPicker(d.id);
                    reactionBar.appendChild(reactButton);
                    msgDiv.appendChild(reactionBar);
                    
                    box.appendChild(msgDiv);
                });
                box.scrollTop = box.scrollHeight;

                snap.forEach(async d => {
                    const m = d.data();
                    if (m.sender !== CURRENT_USER && !m.seenBy?.includes(CURRENT_USER)) {
                        await updateDoc(doc(db, "insta_messages", d.id), { seenBy: arrayUnion(CURRENT_USER), seen: true });
                    }
                });
            });
        }

        window.switchChat = () => {
            const select = document.getElementById('chat-select');
            currentChatId = select.value;
            const linkContainer = document.getElementById('chat-link-container');
            const linkUrl = document.getElementById('chat-link-url');
            if (currentChatId !== 'group') {
                const link = `${window.location.origin}${window.location.pathname}?chat=${encodeURIComponent(currentChatId)}`;
                linkUrl.href = link;
                linkUrl.textContent = link;
                linkContainer.style.display = 'block';
            } else {
                linkContainer.style.display = 'none';
            }
            showChats();
            listenMessages(currentChatId);
        };

        window.createChat = async () => {
            const name = document.getElementById('new-chat-name').value.trim();
            if (!name) return alert('Enter a chat name.');
            const safeName = name.toLowerCase().replace(/[^a-z0-9]+/g, '-').replace(/^-+|-+$/g, '');
            const chatId = `chat-${safeName}-${Date.now()}`;
            try {
                await setDoc(doc(db, 'chats', chatId), { name, createdBy: CURRENT_USER, createdAt: serverTimestamp() });
                const option = document.createElement('option');
                option.value = chatId;
                option.textContent = `Chat: ${name}`;
                document.getElementById('chat-select').appendChild(option);
                document.getElementById('chat-select').value = chatId;
                const link = `${window.location.origin}${window.location.pathname}?chat=${encodeURIComponent(chatId)}`;
                document.getElementById('chat-link-url').href = link;
                document.getElementById('chat-link-url').textContent = link;
                document.getElementById('chat-link-container').style.display = 'block';
                document.getElementById('new-chat-name').value = '';
                switchChat();
            } catch (error) {
                console.error('Create chat error:', error);
                alert('Could not create chat.');
            }
        };

        window.copyChatLink = () => {
            const link = document.getElementById('chat-link-url').href;
            navigator.clipboard.writeText(link).then(() => {
                alert('Chat link copied.');
            }).catch(() => {
                prompt('Copy this link:', link);
            });
        };

        window.sendTweet = async () => {
            const text = document.getElementById('tweet-text').value.trim();
            if (!text) return alert('Write something before tweeting.');
            try {
                await addDoc(collection(db, 'tweets'), {
                    author: CURRENT_USER,
                    text,
                    time: serverTimestamp(),
                    likes: [],
                });
                document.getElementById('tweet-text').value = '';
                showFeed();
            } catch (error) {
                console.error('Tweet error:', error);
                alert('Could not post tweet.');
            }
        };

        window.toggleTweetLike = async (tweetId, currentLikes) => {
            try {
                const tweetRef = doc(db, 'tweets', tweetId);
                if (currentLikes.includes(CURRENT_USER)) {
                    await updateDoc(tweetRef, { likes: arrayRemove(CURRENT_USER) });
                } else {
                    await updateDoc(tweetRef, { likes: arrayUnion(CURRENT_USER) });
                }
            } catch (error) {
                console.error('Like toggle error:', error);
                alert('Could not update like.');
            }
        };

        window.toggleFollow = async (userId) => {
            const followId = `${CURRENT_USER}-${userId}`;
            if (follows.has(userId)) {
                // Unfollow
                const q = query(collection(db, "follows"), where('follower', '==', CURRENT_USER), where('followed', '==', userId));
                const snap = await getDocs(q);
                snap.forEach(async d => await deleteDoc(doc(db, "follows", d.id)));
            } else {
                // Follow
                await addDoc(collection(db, "follows"), { follower: CURRENT_USER, followed: userId });
            }
        };

        window.toggleEmoji = () => {
            const picker = document.getElementById('emoji-picker');
            picker.style.display = picker.style.display === 'none' ? 'block' : 'none';
        };

        window.toggleStickerPicker = () => {
            const picker = document.getElementById('sticker-picker');
            picker.style.display = picker.style.display === 'none' ? 'block' : 'none';
        };

        window.toggleSongPicker = () => {
            const picker = document.getElementById('song-picker');
            picker.style.display = picker.style.display === 'none' ? 'block' : 'none';
        };

        window.showChats = () => {
            document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
            document.querySelector('button[onclick="showChats()"]').classList.add('active');
            document.querySelector('.chat-selector').style.display = 'flex';
            document.getElementById('users-list').style.display = 'none';
            document.getElementById('tweet-feed').style.display = 'none';
            document.getElementById('profile-view').style.display = 'none';
            document.getElementById('messages').style.display = 'block';
            document.getElementById('typing-indicator').style.display = 'block';
            document.querySelector('.input-box').style.display = 'flex';
        };

        window.showUsers = () => {
            document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
            document.querySelector('button[onclick="showUsers()"]').classList.add('active');
            document.querySelector('.chat-selector').style.display = 'none';
            document.getElementById('users-list').style.display = 'block';
            document.getElementById('tweet-feed').style.display = 'none';
            document.getElementById('profile-view').style.display = 'none';
            document.getElementById('messages').style.display = 'none';
            document.getElementById('typing-indicator').style.display = 'none';
            document.querySelector('.input-box').style.display = 'none';
        };

        window.showProfile = () => {
            document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
            document.querySelector('button[onclick="showProfile()"]').classList.add('active');
            document.querySelector('.chat-selector').style.display = 'none';
            document.getElementById('users-list').style.display = 'none';
            document.getElementById('tweet-feed').style.display = 'none';
            document.getElementById('profile-view').style.display = 'block';
            document.getElementById('messages').style.display = 'none';
            document.getElementById('typing-indicator').style.display = 'none';
            document.querySelector('.input-box').style.display = 'none';
            viewProfile(CURRENT_USER);
        };

        window.showFeed = () => {
            document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
            document.querySelector('button[onclick="showFeed()"]').classList.add('active');
            document.querySelector('.chat-selector').style.display = 'none';
            document.getElementById('users-list').style.display = 'none';
            document.getElementById('tweet-feed').style.display = 'block';
            document.getElementById('profile-view').style.display = 'none';
            document.getElementById('messages').style.display = 'none';
            document.getElementById('typing-indicator').style.display = 'none';
            document.querySelector('.input-box').style.display = 'none';
        };

        window.viewProfile = async (userId) => {
            document.getElementById('profile-username').textContent = userId;
            const snap = await getDoc(doc(db, "profiles", userId));
            if (snap.exists()) {
                const data = snap.data();
                document.getElementById('profile-pic').src = data.picUrl || "https://via.placeholder.com/100";
                document.getElementById('bio-input').value = data.bio || "";
            } else {
                document.getElementById('profile-pic').src = "https://via.placeholder.com/100";
                document.getElementById('bio-input').value = "";
            }
            const isOwn = userId === CURRENT_USER;
            document.getElementById('pic-input').style.display = isOwn ? 'block' : 'none';
            document.getElementById('bio-input').readOnly = !isOwn;
            document.querySelector('.save-btn').style.display = isOwn ? 'block' : 'none';
            document.getElementById('profile-back-btn').style.display = isOwn ? 'none' : 'block';
            document.getElementById('change-pic-btn').style.display = isOwn ? 'block' : 'none';
            showProfile();
        };

        window.saveProfile = async () => {
            const bio = document.getElementById('bio-input').value;
            let picUrl = profileData.picUrl;
            const file = document.getElementById('pic-input').files[0];
            if (file) {
                const storageRef = ref(storage, `profiles/${CURRENT_USER}_${file.name}`);
                await uploadBytes(storageRef, file);
                picUrl = await getDownloadURL(storageRef);
            }
            await setDoc(doc(db, "profiles", CURRENT_USER), { bio, picUrl });
            alert("Profile saved!");
        };

        window.previewPic = () => {
            const file = document.getElementById('pic-input').files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    document.getElementById('profile-pic').src = e.target.result;
                };
                reader.readAsDataURL(file);
            }
        };

        if ('serviceWorker' in navigator) {
            window.addEventListener('load', () => {
                navigator.serviceWorker.register('service-worker.js')
                    .then(reg => console.log('SW registered:', reg.scope))
                    .catch(err => console.error('SW registration failed:', err));
            });
        }

    </script>
</body>
</html>
