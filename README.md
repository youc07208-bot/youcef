<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Direct Chat & Call</title>
  <style>
    :root {
      --bg: #000000;
      --surface: #121212;
      --surface-2: #262626;
      --border: #2a2a2a;
      --text: #f5f5f5;
      --text-muted: #a8a8a8;
      --primary: #0095f6;
      --primary-grad: linear-gradient(45deg, #f09433 0%, #e6683c 25%, #dc2743 50%, #cc2366 75%, #bc1888 100%);
      --msg-me: #3797f0;
      --msg-other: #262626;
      --danger: #ed4956;
      --success: #2ecc71;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
    body { background: var(--bg); color: var(--text); height: 100dvh; display: flex; justify-content: center; overflow: hidden; }

    .app-container {
      width: 100%;
      max-width: 480px;
      height: 100dvh;
      background: var(--bg);
      border-left: 1px solid var(--border);
      border-right: 1px solid var(--border);
      display: flex;
      flex-direction: column;
      position: relative;
    }

    .hidden { display: none !important; }

    /* شاشة تسجيل الدخول */
    #authScreen {
      flex: 1;
      display: flex;
      flex-direction: column;
      justify-content: center;
      padding: 30px 24px;
      gap: 20px;
    }
    .logo-title {
      font-size: 28px;
      font-weight: 800;
      text-align: center;
      background: var(--primary-grad);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin-bottom: 10px;
    }
    .input-box {
      width: 100%;
      padding: 14px 16px;
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: 12px;
      color: var(--text);
      font-size: 15px;
      outline: none;
    }
    .input-box:focus { border-color: var(--primary); }
    .btn-primary {
      width: 100%;
      padding: 14px;
      background: var(--primary);
      color: #fff;
      border: none;
      border-radius: 12px;
      font-size: 15px;
      font-weight: 600;
      cursor: pointer;
    }
    .btn-primary:active { opacity: 0.85; }

    /* الشاشة الرئيسية */
    #homeScreen { flex: 1; display: flex; flex-direction: column; overflow: hidden; }
    .top-bar {
      padding: 14px 16px;
      border-bottom: 1px solid var(--border);
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .user-badge { display: flex; flex-direction: column; }
    .user-badge strong { font-size: 16px; }
    .uid-pill {
      display: inline-flex;
      align-items: center;
      gap: 6px;
      background: var(--surface-2);
      padding: 4px 10px;
      border-radius: 20px;
      font-size: 12px;
      color: var(--primary);
      margin-top: 4px;
      cursor: pointer;
    }
    .logout-btn { background: none; border: none; color: var(--danger); font-size: 13px; cursor: pointer; }

    .search-section { padding: 12px 16px; display: flex; gap: 8px; }
    .search-section input { flex: 1; padding: 10px 14px; border-radius: 10px; background: var(--surface-2); border: none; color: #fff; outline: none; }
    .search-section button { padding: 0 16px; border-radius: 10px; background: var(--primary); color: #fff; border: none; font-weight: 600; cursor: pointer; }

    .friends-list { flex: 1; overflow-y: auto; padding: 8px 16px; }
    .section-label { font-size: 13px; color: var(--text-muted); margin-bottom: 12px; font-weight: 600; }
    .friend-item {
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 10px 0;
      cursor: pointer;
      border-bottom: 1px solid #1a1a1a;
    }
    .avatar {
      width: 48px;
      height: 48px;
      border-radius: 50%;
      background: var(--primary-grad);
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 700;
      font-size: 18px;
      color: #fff;
      flex-shrink: 0;
    }
    .friend-info { flex: 1; }
    .friend-info h4 { font-size: 15px; margin-bottom: 3px; }
    .friend-info span { font-size: 12px; color: var(--text-muted); }

    /* شاشة المحادثة */
    #chatScreen { flex: 1; display: flex; flex-direction: column; height: 100%; }
    .chat-header {
      padding: 12px 16px;
      border-bottom: 1px solid var(--border);
      display: flex;
      align-items: center;
      justify-content: space-between;
      background: var(--bg);
    }
    .chat-user { display: flex; align-items: center; gap: 10px; }
    .back-btn { background: none; border: none; color: #fff; font-size: 22px; cursor: pointer; padding-left: 6px; }
    .chat-actions { display: flex; gap: 14px; }
    .icon-btn {
      background: var(--surface-2);
      border: none;
      color: #fff;
      width: 38px;
      height: 38px;
      border-radius: 50%;
      font-size: 17px;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .icon-btn:hover { background: var(--primary); }

    .messages-area {
      flex: 1;
      overflow-y: auto;
      padding: 16px;
      display: flex;
      flex-direction: column;
      gap: 8px;
    }
    .msg-bubble {
      max-width: 75%;
      padding: 10px 14px;
      border-radius: 18px;
      font-size: 14px;
      line-height: 1.4;
      word-wrap: break-word;
    }
    .msg-me {
      align-self: flex-start;
      background: var(--msg-me);
      color: #fff;
      border-bottom-right-radius: 4px;
    }
    .msg-other {
      align-self: flex-end;
      background: var(--msg-other);
      color: var(--text);
      border-bottom-left-radius: 4px;
    }

    .chat-input-bar {
      padding: 10px 14px;
      border-top: 1px solid var(--border);
      display: flex;
      align-items: center;
      gap: 8px;
      background: var(--bg);
    }
    .chat-input-bar input {
      flex: 1;
      padding: 11px 16px;
      border-radius: 22px;
      border: 1px solid var(--border);
      background: var(--surface);
      color: #fff;
      outline: none;
      font-size: 14px;
    }
    .send-btn {
      background: none;
      border: none;
      color: var(--primary);
      font-weight: 700;
      font-size: 15px;
      cursor: pointer;
      padding: 0 6px;
    }

    /* شاشة المكالمة الصوتية والمرئية */
    #callOverlay {
      position: absolute;
      inset: 0;
      background: rgba(10, 10, 10, 0.96);
      z-index: 50;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      align-items: center;
      padding: 40px 20px;
    }
    .call-info { text-align: center; z-index: 2; }
    .call-info h3 { font-size: 22px; margin-bottom: 6px; }
    .call-info p { color: var(--text-muted); font-size: 14px; }

    .video-grid {
      position: relative;
      width: 100%;
      flex: 1;
      margin: 20px 0;
      border-radius: 16px;
      overflow: hidden;
      background: #1a1a1a;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    #remoteVideo { width: 100%; height: 100%; object-fit: cover; }
    #localVideo {
      position: absolute;
      bottom: 14px;
      left: 14px;
      width: 100px;
      height: 140px;
      object-fit: cover;
      border-radius: 12px;
      border: 2px solid #fff;
      background: #000;
    }

    .call-controls { display: flex; gap: 20px; z-index: 2; }
    .ctrl-btn {
      width: 56px;
      height: 56px;
      border-radius: 50%;
      border: none;
      font-size: 22px;
      color: #fff;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .btn-mute { background: var(--surface-2); }
    .btn-end { background: var(--danger); }
    .btn-accept { background: var(--success); }
  </style>
</head>
<body>

<div class="app-container">

  <div id="authScreen">
    <div class="logo-title">InstaConnect</div>
    <p style="text-align:center; color:var(--text-muted); font-size:14px;">أدخل اسمك للحصول على معرّف ID خاص بك للتواصل الفوري</p>
    <input id="nameInput" class="input-box" type="text" placeholder="اكتب اسمك هنا..." />
    <button id="loginBtn" class="btn-primary">دخول وإنشاء ID</button>
  </div>

  <div id="homeScreen" class="hidden">
    <div class="top-bar">
      <div class="user-badge">
        <strong id="myNameDisplay">المستخدم</strong>
        <div class="uid-pill" id="copyUidBtn" title="اضغط لنسخ الـ ID">
          <span id="myUidDisplay">ID: U-00000</span>
          <span>📋</span>
        </div>
      </div>
      <button id="logoutBtn" class="logout-btn">خروج</button>
    </div>

    <div class="search-section">
      <input id="searchUidInput" type="text" placeholder="ابحث بـ ID صديقك (مثال: U-48219)" />
      <button id="searchBtn">بحث</button>
    </div>

    <div class="friends-list">
      <div class="section-label">المحادثات المباشرة</div>
      <div id="friendsContainer"></div>
    </div>
  </div>

  <div id="chatScreen" class="hidden">
    <div class="chat-header">
      <div class="chat-user">
        <button id="backBtn" class="back-btn">→</button>
        <div class="avatar" id="chatAvatar" style="width:38px; height:38px; font-size:15px;">ص</div>
        <div>
          <strong id="chatFriendName" style="font-size:15px; display:block;">اسم الصديق</strong>
          <span id="chatFriendUid" style="font-size:11px; color:var(--text-muted);">U-00000</span>
        </div>
      </div>
      <div class="chat-actions">
        <button id="audioCallBtn" class="icon-btn" title="مكالمة صوتية">📞</button>
        <button id="videoCallBtn" class="icon-btn" title="مكالمة فيديو">📹</button>
      </div>
    </div>

    <div id="messagesArea" class="messages-area"></div>

    <form id="msgForm" class="chat-input-bar">
      <input id="msgInput" type="text" placeholder="اكتب رسالة..." autocomplete="off" />
      <button type="submit" class="send-btn">إرسال</button>
    </form>
  </div>

  <div id="callOverlay" class="hidden">
    <div class="call-info">
      <h3 id="callPeerName">جاري الاتصال...</h3>
      <p id="callStatusText">يرن...</p>
    </div>

    <div class="video-grid" id="videoContainer">
      <video id="remoteVideo" autoplay playsinline></video>
      <video id="localVideo" autoplay muted playsinline></video>
    </div>

    <div class="call-controls">
      <button id="acceptCallBtn" class="ctrl-btn btn-accept hidden">📞</button>
      <button id="muteBtn" class="ctrl-btn btn-mute">🎤</button>
      <button id="endCallBtn" class="ctrl-btn btn-end">📵</button>
    </div>
  </div>

</div>

<script type="module">
  import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

  // ضع بيانات مشروعك من Supabase هنا
  const SUPABASE_URL = "https://YOUR_PROJECT_ID.supabase.co";
  const SUPABASE_ANON_KEY = "YOUR_SUPABASE_ANON_KEY";
  const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

  let currentUser = JSON.parse(localStorage.getItem("insta_chat_user")) || null;
  let activeFriend = null;
  let currentRoomId = null;
  let msgSubscription = null;
  let personalSignalChannel = null;

  let pc = null;
  let localStream = null;
  let incomingOffer = null;
  let incomingCaller = null;
  let isVideoCall = false;
  let isMuted = false;
  const pendingCandidates = [];

  const rtcConfig = {
    iceServers: [{ urls: "stun:stun.l.google.com:19302" }]
  };

  const authScreen = document.getElementById("authScreen");
  const homeScreen = document.getElementById("homeScreen");
  const chatScreen = document.getElementById("chatScreen");
  const callOverlay = document.getElementById("callOverlay");

  function generateUID() {
    return "U-" + Math.floor(10000 + Math.random() * 90000);
  }

  function getSharedRoomId(uid1, uid2) {
    return [uid1, uid2].sort().join("_");
  }

  async function init() {
    if (currentUser) {
      showHome();
    } else {
      authScreen.classList.remove("hidden");
    }
  }

  document.getElementById("loginBtn").addEventListener("click", async () => {
    const name = document.getElementById("nameInput").value.trim();
    if (!name) return alert("يرجى كتابة اسمك أولاً");

    const uid = generateUID();
    const { error } = await supabase.from("users").insert([{ uid, name }]);
    if (error) return alert("خطأ في الاتصال بـ Supabase: " + error.message);

    currentUser = { uid, name };
    localStorage.setItem("insta_chat_user", JSON.stringify(currentUser));
    showHome();
  });

  function showHome() {
    authScreen.classList.add("hidden");
    chatScreen.classList.add("hidden");
    homeScreen.classList.remove("hidden");

    document.getElementById("myNameDisplay").textContent = currentUser.name;
    document.getElementById("myUidDisplay").textContent = `ID: ${currentUser.uid}`;

    subscribeToIncomingCalls();
    renderSavedFriends();
  }

  document.getElementById("copyUidBtn").addEventListener("click", () => {
    navigator.clipboard.writeText(currentUser.uid);
    alert("تم نسخ الـ ID الخاص بك: " + currentUser.uid);
  });

  document.getElementById("logoutBtn").addEventListener("click", () => {
    localStorage.removeItem("insta_chat_user");
    location.reload();
  });

  document.getElementById("searchBtn").addEventListener("click", async () => {
    const targetUid = document.getElementById("searchUidInput").value.trim().toUpperCase();
    if (!targetUid) return;
    if (targetUid === currentUser.uid) return alert("هذا هو الـ ID الخاص بك!");

    const { data, error } = await supabase
      .from("users")
      .select("*")
      .eq("uid", targetUid)
      .single();

    if (error || !data) {
      return alert("لم يتم العثور على مستخدم بهذا الـ ID");
    }

    saveFriendLocally(data);
    openChat(data);
  });

  function saveFriendLocally(friend) {
    const list = JSON.parse(localStorage.getItem("insta_friends_" + currentUser.uid)) || [];
    if (!list.some(f => f.uid === friend.uid)) {
      list.unshift(friend);
      localStorage.setItem("insta_friends_" + currentUser.uid, JSON.stringify(list));
    }
    renderSavedFriends();
  }

  function renderSavedFriends() {
    const list = JSON.parse(localStorage.getItem("insta_friends_" + currentUser.uid)) || [];
    const container = document.getElementById("friendsContainer");
    container.innerHTML = list.length === 0
      ? `<p style="color:var(--text-muted); font-size:13px;">ابحث عن ID صديقك بالأعلى لبدء المحادثة.</p>`
      : "";

    list.forEach(friend => {
      const div = document.createElement("div");
      div.className = "friend-item";
      div.innerHTML = `
        <div class="avatar">${friend.name.charAt(0)}</div>
        <div class="friend-info">
          <h4>${friend.name}</h4>
          <span>${friend.uid} • اضغط للمراسلة والاتصال</span>
        </div>
      `;
      div.onclick = () => openChat(friend);
      container.appendChild(div);
    });
  }

  async function openChat(friend) {
    activeFriend = friend;
    currentRoomId = getSharedRoomId(currentUser.uid, friend.uid);

    homeScreen.classList.add("hidden");
    chatScreen.classList.remove("hidden");

    document.getElementById("chatFriendName").textContent = friend.name;
    document.getElementById("chatFriendUid").textContent = friend.uid;
    document.getElementById("chatAvatar").textContent = friend.name.charAt(0);

    const messagesArea = document.getElementById("messagesArea");
    messagesArea.innerHTML = "";

    const { data: msgs } = await supabase
      .from("messages")
      .select("*")
      .eq("room_id", currentRoomId)
      .order("created_at", { ascending: true });

    if (msgs) msgs.forEach(appendMessage);

    if (msgSubscription) supabase.removeChannel(msgSubscription);
    msgSubscription = supabase
      .channel("room-msgs-" + currentRoomId)
      .on("postgres_changes", {
        event: "INSERT",
        schema: "public",
        table: "messages",
        filter: `room_id=eq.${currentRoomId}`
      }, (payload) => {
        appendMessage(payload.new);
      })
      .subscribe();
  }

  function appendMessage(msg) {
    const messagesArea = document.getElementById("messagesArea");
    const div = document.createElement("div");
    div.className = "msg-bubble " + (msg.sender_uid === currentUser.uid ? "msg-me" : "msg-other");
    div.textContent = msg.content;
    messagesArea.appendChild(div);
    messagesArea.scrollTop = messagesArea.scrollHeight;
  }

  document.getElementById("msgForm").addEventListener("submit", async (e) => {
    e.preventDefault();
    const input = document.getElementById("msgInput");
    const text = input.value.trim();
    if (!text || !activeFriend) return;

    input.value = "";
    await supabase.from("messages").insert([{
      room_id: currentRoomId,
      sender_uid: currentUser.uid,
      sender_name: currentUser.name,
      content: text
    }]);
  });

  document.getElementById("backBtn").addEventListener("click", () => {
    if (msgSubscription) supabase.removeChannel(msgSubscription);
    activeFriend = null;
    showHome();
  });

  function subscribeToIncomingCalls() {
    if (personalSignalChannel) supabase.removeChannel(personalSignalChannel);

    personalSignalChannel = supabase.channel(`user-signal:${currentUser.uid}`);
    personalSignalChannel
      .on("broadcast", { event: "rtc" }, async ({ payload }) => {
        if (payload.type === "offer") {
          incomingOffer = payload.offer;
          incomingCaller = payload.caller;
          isVideoCall = payload.video;

          saveFriendLocally(incomingCaller);

          document.getElementById("callPeerName").textContent = incomingCaller.name;
          document.getElementById("callStatusText").textContent = isVideoCall ? "مكالمة فيديو واردة..." : "مكالمة صوتية واردة...";
          document.getElementById("videoContainer").style.display = isVideoCall ? "flex" : "none";
          document.getElementById("acceptCallBtn").classList.remove("hidden");
          callOverlay.classList.remove("hidden");
        } else if (payload.type === "answer" && pc) {
          await pc.setRemoteDescription(new RTCSessionDescription(payload.answer));
          await flushCandidates();
          document.getElementById("callStatusText").textContent = "متصل الآن";
        } else if (payload.type === "candidate") {
          if (pc && pc.remoteDescription) {
            await pc.addIceCandidate(new RTCIceCandidate(payload.candidate));
          } else {
            pendingCandidates.push(payload.candidate);
          }
        } else if (payload.type === "hangup") {
          endCall(false);
        }
      })
      .subscribe();
  }

  function sendSignalToPeer(targetUid, data) {
    const ch = supabase.channel(`user-signal:${targetUid}`);
    ch.subscribe((status) => {
      if (status === "SUBSCRIBED") {
        ch.send({ type: "broadcast", event: "rtc", payload: data });
      }
    });
  }

  async function setupPeer(targetUid, withVideo) {
    localStream = await navigator.mediaDevices.getUserMedia({ audio: true, video: withVideo });
    document.getElementById("localVideo").srcObject = localStream;

    pc = new RTCPeerConnection(rtcConfig);
    localStream.getTracks().forEach(track => pc.addTrack(track, localStream));

    pc.ontrack = (e) => {
      document.getElementById("remoteVideo").srcObject = e.streams[0];
    };

    pc.onicecandidate = (e) => {
      if (e.candidate) {
        sendSignalToPeer(targetUid, { type: "candidate", candidate: e.candidate });
      }
    };
  }

  async function flushCandidates() {
    while (pendingCandidates.length > 0 && pc && pc.remoteDescription) {
      await pc.addIceCandidate(new RTCIceCandidate(pendingCandidates.shift()));
    }
  }

  async function startOutgoingCall(withVideo) {
    if (!activeFriend) return;
    isVideoCall = withVideo;
    incomingCaller = activeFriend;

    document.getElementById("callPeerName").textContent = activeFriend.name;
    document.getElementById("callStatusText").textContent = "جاري الاتصال...";
    document.getElementById("videoContainer").style.display = withVideo ? "flex" : "none";
    document.getElementById("acceptCallBtn").classList.add("hidden");
    callOverlay.classList.remove("hidden");

    await setupPeer(activeFriend.uid, withVideo);
    const offer = await pc.createOffer();
    await pc.setLocalDescription(offer);

    sendSignalToPeer(activeFriend.uid, {
      type: "offer",
      offer,
      caller: currentUser,
      video: withVideo
    });
  }

  document.getElementById("audioCallBtn").addEventListener("click", () => startOutgoingCall(false));
  document.getElementById("videoCallBtn").addEventListener("click", () => startOutgoingCall(true));

  document.getElementById("acceptCallBtn").addEventListener("click", async () => {
    document.getElementById("acceptCallBtn").classList.add("hidden");
    document.getElementById("callStatusText").textContent = "متصل الآن";

    await setupPeer(incomingCaller.uid, isVideoCall);
    await pc.setRemoteDescription(new RTCSessionDescription(incomingOffer));
    await flushCandidates();

    const answer = await pc.createAnswer();
    await pc.setLocalDescription(answer);

    sendSignalToPeer(incomingCaller.uid, {
      type: "answer",
      answer
    });
  });

  document.getElementById("muteBtn").addEventListener("click", () => {
    if (!localStream) return;
    isMuted = !isMuted;
    localStream.getAudioTracks().forEach(t => (t.enabled = !isMuted));
    document.getElementById("muteBtn").style.background = isMuted ? "var(--danger)" : "var(--surface-2)";
  });

  function endCall(notify = true) {
    if (notify && incomingCaller) {
      sendSignalToPeer(incomingCaller.uid, { type: "hangup" });
    }
    if (pc) { pc.close(); pc = null; }
    if (localStream) { localStream.getTracks().forEach(t => t.stop()); localStream = null; }
    incomingOffer = null;
    callOverlay.classList.add("hidden");
  }

  document.getElementById("endCallBtn").addEventListener("click", () => endCall(true));

  init();
</script>
</body>
</html>
