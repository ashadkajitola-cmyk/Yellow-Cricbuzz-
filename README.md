<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cricket Central</title>
    
    <!-- Firebase App & Realtime Database (Compat SDK) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-database-compat.js"></script>

    <style>
        :root {
            --primary: #009688;
            --primary-dark: #00796b;
            --bg-light: #f4f6f9;
            --card-bg: #ffffff;
            --text-main: #333333;
            --text-muted: #666666;
            --accent: #ff9800;
            --danger: #dc3545;
        }
        body { 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
            background: var(--bg-light); 
            margin: 0; 
            padding: 0; 
            color: var(--text-main); 
        }
        .app-header {
            background: var(--primary);
            color: white;
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        .app-header h2 { margin: 0; font-size: 20px; }
        .admin-badge-btn {
            background: white;
            color: var(--primary);
            border: none;
            padding: 6px 12px;
            border-radius: 4px;
            font-weight: bold;
            cursor: pointer;
        }
        .tabs-container {
            background: var(--primary-dark);
            display: flex;
            justify-content: space-around;
            padding: 0 10px;
            overflow-x: auto;
        }
        .tab-btn {
            background: none;
            border: none;
            color: rgba(255,255,255,0.7);
            padding: 12px 15px;
            font-weight: bold;
            cursor: pointer;
            font-size: 14px;
            text-transform: uppercase;
            white-space: nowrap;
        }
        .tab-btn.active {
            color: white;
            border-bottom: 3px solid white;
        }
        .container { max-width: 500px; margin: auto; padding: 15px; }
        .card { 
            background: var(--card-bg); 
            padding: 15px; 
            border-radius: 8px; 
            margin-bottom: 15px; 
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            border: 1px solid #e0e0e0;
        }
        input, select, button { 
            width: 100%; 
            padding: 10px; 
            margin-top: 8px; 
            border: 1px solid #ccc; 
            border-radius: 6px; 
            box-sizing: border-box; 
            font-size: 14px;
        }
        button.action-btn { 
            background: var(--primary); 
            color: white; 
            border: none; 
            font-weight: bold; 
            cursor: pointer; 
        }
        button.action-btn:hover { background: var(--primary-dark); }
        .logout-btn { background: var(--danger); color: white; margin-top: 10px; border: none; font-weight: bold; padding: 10px; border-radius: 6px; cursor: pointer;}
        .hidden { display: none !important; }
        
        .match-box {
            background: #fff;
            border: 1px solid #e0e0e0;
            border-radius: 8px;
            padding: 12px;
            margin-bottom: 10px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.05);
        }
        .match-info-top {
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            color: var(--text-muted);
            margin-bottom: 6px;
        }
        .match-teams {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-weight: bold;
            font-size: 15px;
            margin: 8px 0;
        }
        .match-toss-banner {
            background: #e0f2f1;
            color: #00796b;
            padding: 5px 8px;
            font-size: 12px;
            border-radius: 4px;
            margin-top: 6px;
            font-weight: bold;
        }
        .match-result-banner {
            background: #ffebee;
            color: #c62828;
            padding: 5px 8px;
            font-size: 12px;
            border-radius: 4px;
            margin-top: 6px;
            font-weight: bold;
        }

        .pro-table-container { background: #fff; border-radius: 8px; overflow: hidden; border: 1px solid #e0e0e0; }
        .pro-table-header { background: #e0f2f1; color: var(--primary-dark); padding: 10px 12px; font-weight: bold; font-size: 15px; display: flex; align-items: center; gap: 8px; }
        .ptable { width: 100%; border-collapse: collapse; font-size: 13px; }
        .ptable th { background: #f4f6f9; color: var(--text-muted); font-weight: 600; padding: 10px 6px; border-bottom: 1px solid #e0e0e0; border-top: 1px solid #e0e0e0; text-align: center; }
        .ptable th:nth-child(2) { text-align: left; padding-left: 10px; }
        .ptable td { padding: 10px 6px; text-align: center; border-bottom: 1px solid #f0f0f0; color: var(--text-main); }
        .ptable td:nth-child(2) { text-align: left; padding-left: 10px; font-weight: 600; }
        .team-badge-row { display: flex; align-items: center; gap: 8px; }
        .team-mini-logo { width: 22px; height: 22px; background: #e0e0e0; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 10px; font-weight: bold; color: #333; text-transform: uppercase; }
        
        .plan-box { border: 1px solid var(--primary); padding: 10px; border-radius: 6px; margin-top: 8px; background: #f9f9f9; }
        .admin-section-box { background: #fffde7; border: 1px solid #ffe082; padding: 12px; border-radius: 8px; margin-bottom: 15px; }
        .admin-section-box h4 { margin-top: 0; color: #f57c00; font-size: 15px; border-bottom: 1px dashed #ffe082; padding-bottom: 5px; }
    </style>
</head>
<body>

    <div class="app-header">
        <h2>Cricket Central</h2>
        <button type="button" class="admin-badge-btn" id="adminBtn">Admin</button>
    </div>

    <div id="appTabs" class="tabs-container hidden">
        <button type="button" class="tab-btn active" data-tab="matches">Matches</button>
        <button type="button" class="tab-btn" data-tab="points">Points Table</button>
        <button type="button" class="tab-btn" data-tab="groups">Groups</button>
        <button type="button" class="tab-btn" data-tab="wallet">Wallet</button>
    </div>

<div class="container">
    <!-- LOGIN SCREEN (Direct Number Login) -->
    <div id="loginSection" class="card">
        <h3>Cricket App Login</h3>
        <p style="text-align: center; font-size: 13px; color: var(--text-muted);">Aage badhne ke liye apna phone number dalein:<br><span style="color: var(--primary); font-weight: bold;">(Naye number par 15 Free Points milenge!)</span></p>
        
        <div id="phoneStep">
            <input type="tel" id="userPhoneInput" placeholder="Apna Mobile Number Dalein" maxlength="10">
            <button type="button" class="action-btn" id="loginBtn" style="background: #2e7d32; margin-top: 10px;">Login Karein</button>
        </div>
    </div>

    <!-- MAIN DASHBOARD CONTAINER -->
    <div id="dashboardSection" class="hidden">
        
        <!-- TAB 1: MATCHES -->
        <div id="tabMatches" class="tab-content">
            <div class="card">
                <h3>📅 Live & Upcoming Schedule</h3>
                <div id="scheduleList"></div>
            </div>
        </div>

        <!-- TAB 2: POINTS TABLE -->
        <div id="tabPoints" class="tab-content hidden">
            <div id="pointsTablesDisplayContainer"></div>
        </div>

        <!-- TAB 3: GROUPS -->
        <div id="tabGroups" class="tab-content hidden">
            <div class="card">
                <h3>👥 Group Management</h3>
                <p style="font-size: 12px; color: var(--text-muted);">Aap unlimited teams ke sath kitne bhi groups bana sakte hain.</p>
                <div id="groupsContainer" style="margin-top: 10px;"></div>
            </div>
        </div>

        <!-- TAB 4: WALLET & SUBSCRIPTION -->
        <div id="tabWallet" class="tab-content hidden">
            <div class="card" style="background: #e0f2f1;">
                <h3 style="color: var(--primary-dark);">💰 Aapka Wallet</h3>
                <p style="font-size: 16px; text-align: center;"><strong>Available Points:</strong> <span id="userPoints" style="color: var(--primary); font-weight: bold; font-size: 20px;">0</span></p>
                <p id="subStatus" style="text-align: center; font-weight: bold; color: #2e7d32; font-size: 13px;"></p>
            </div>

            <!-- PRICE LIST INFO -->
            <div class="card" style="background: #fff8e1; border: 1px solid #ffe082;">
                <h4 style="margin-top:0; color:#f57c00;">🏷️ Special Point Packages</h4>
                <p style="margin: 4px 0; font-size: 13px;"><strong>80₹</strong> = 400 Points</p>
                <p style="margin: 4px 0; font-size: 13px;"><strong>230₹</strong> = 8000 Points</p>
                <p style="margin: 8px 0 0 0; font-size: 13px; color: #d32f2f; font-weight: bold; text-align: center;">Point purchase karne ke liye 9569981484 par sampark karein.</p>
            </div>

            <div class="card">
                <h3>⭐ Subscription Plans</h3>
                <div class="plan-box">
                    <p style="margin: 0 0 5px 0;"><strong>30 Minutes Plan:</strong> 149 Points</p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="149" data-plan="30 Minutes Plan" data-time="30">Buy 30 Mins Plan</button>
                </div>
                <div class="plan-box">
                    <p style="margin: 0 0 5px 0;"><strong>Half Monthly Plan:</strong> 400 Points</p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="400" data-plan="Half Monthly Plan" data-days="15">Buy Half Monthly</button>
                </div>
                <div class="plan-box">
                    <p style="margin: 0 0 5px 0;"><strong>Monthly Plan:</strong> 600 Points</p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="600" data-plan="Monthly Plan" data-days="30">Buy Monthly</button>
                </div>
                <div class="plan-box">
                    <p style="margin: 0 0 5px 0;"><strong>Half Yearly Plan:</strong> 6000 Points</p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="6000" data-plan="Half Yearly Plan" data-days="180">Buy Half Yearly</button>
                </div>
                <div class="plan-box">
                    <p style="margin: 0 0 5px 0;"><strong>Yearly Plan:</strong> 8000 Points</p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="8000" data-plan="Yearly Plan" data-days="365">Buy Yearly</button>
                </div>
            </div>
        </div>

        <!-- ADMIN PANEL SECTION -->
        <div id="adminPanelSection" class="card hidden" style="border: 2px solid var(--accent); background: #fff;">
            <h3 style="color: #f57c00; margin-top:0;">👑 Admin Control Panel</h3>
            
            <!-- DIRECT POINTS SENDER -->
            <div id="mainAdminCodeBox" class="admin-section-box hidden">
                <h4>1. User ke Wallet mein Points Bhejein (Admin Only)</h4>
                <input type="tel" id="adminTargetPhone" placeholder="User ka 10-digit Mobile Number dalein" maxlength="10">
                <input type="number" id="adminSendPoints" placeholder="Kitne points bhejne hain? (e.g. 500)">
                <button type="button" class="action-btn" id="sendPointsToUserBtn" style="background: #f57c00; margin-top:8px;">User ko Points Bhejein</button>
            </div>

            <!-- OPTION 2: MATCH SCHEDULE & RESULT UPDATE -->
            <div class="admin-section-box">
                <h4>2. Match Schedule & Result Update</h4>
                <input type="hidden" id="editMatchId" value="">
                <input type="text" id="seriesName" placeholder="Series Name (jaise: Sa20, 2027)">
                
                <label style="font-size: 12px; font-weight: bold; color: #333; display: block; margin-top: 6px;">Series ka Type Chunein:</label>
                <select id="seriesTypeOption">
                    <option value="same">1. Usi series ka match hai (Bina gap ke)</option>
                    <option value="new">2. New series ka match hai (Gap ke sath)</option>
                </select>

                <input type="text" id="matchFormat" placeholder="Format (jaise: 1st Match / T20)">
                <input type="text" id="team1" placeholder="Team 1 (jaise: DSG)">
                <input type="text" id="team2" placeholder="Team 2 (jaise: JSK)">
                <input type="text" id="venue" placeholder="Venue / Stadium">
                <input type="text" id="matchTossUpdate" placeholder="Toss Update (jaise: DSG won toss & elected to bat)">
                <button type="button" class="action-btn" id="saveMatchBtn" style="margin-top:8px;">Match Save Karein</button>

                <!-- ADMIN MATCH LIST FOR EDIT/DELETE -->
                <h5 style="margin: 12px 0 5px 0; color: #333;">Existing Matches (Edit / Delete)</h5>
                <div id="adminMatchesList" style="max-height: 150px; overflow-y: auto; border: 1px solid #ccc; padding: 5px; border-radius: 4px; background: #fff;"></div>

                <h5 style="margin: 12px 0 5px 0; color: #333;">Match Result & Scores Update</h5>
                <select id="matchSelectForUpdate"></select>
                <input type="text" id="matchWinner" placeholder="Match Winner (jaise: DSG won by 6 wickets)">
                <input type="text" id="team1ScoreDetails" placeholder="Team 1 Score (jaise: 180/4 in 20 ov)">
                <input type="text" id="team2ScoreDetails" placeholder="Team 2 Score (jaise: 175/6 in 20 ov)">
                <button type="button" class="action-btn" id="updateResultBtn" style="background: #0284c7; margin-top:8px;">Result Update Karein</button>
            </div>

            <!-- OPTION 3: GROUP CREATION -->
            <div class="admin-section-box">
                <h4>3. Group Creation (Unlimited Teams)</h4>
                <input type="text" id="groupNameInput" placeholder="Group Name (jaise: Sa20, 2027)">
                <input type="text" id="groupTeamsInput" placeholder="Teams comma se alag karein (jaise: DSG, JSK, MICT)">
                <button type="button" class="action-btn" id="createGroupBtn" style="margin-top:8px;">Group Banayein</button>
                <div id="adminGroupsList" style="margin-top: 10px;"></div>
            </div>

            <!-- OPTION 4: POINTS TABLE MANAGEMENT -->
            <div class="admin-section-box">
                <h4>4. Points Table Management</h4>
                
                <label style="font-size: 12px; font-weight: bold; color: #333; display: block; margin-top: 5px;">Aap kya karna chahte hain?</label>
                <select id="tableActionMode" style="margin-bottom: 8px;">
                    <option value="existing">Existing Table mein Team/Match Update Karein</option>
                    <option value="new">Nayi Points Table Banayein</option>
                </select>

                <!-- EXISTING TABLE WRAPPER -->
                <div id="existingTableSection">
                    <label style="font-size: 12px; font-weight: bold; color: #333; display: block; margin-top: 5px;">Points Table Chunein:</label>
                    <select id="ptGroupSelect">
                        <option value="">-- Table Chunein --</option>
                    </select>

                    <div style="margin-top: 6px;">
                        <label style="font-size: 12px; color: #555;">Nayi Team Jodni hai is table mein? (Optional)</label>
                        <input type="text" id="extraTeamInput" placeholder="Nayi Team ka naam (jaise: RCB)">
                        <button type="button" class="action-btn" id="addTeamToTableBtn" style="background: #607d8b; margin-top:4px;">Table mein Team Jodein</button>
                    </div>

                    <label style="font-size: 12px; font-weight: bold; color: #333; display: block; margin-top: 8px;">Team 1 Chunein:</label>
                    <select id="ptTeam1Select">
                        <option value="">Pehle Table Chunein</option>
                    </select>
                    <input type="text" id="ptTeam1ScoreInput" placeholder="Team 1 Score (jaise: 180/4 in 20 ov)">

                    <label style="font-size: 12px; font-weight: bold; color: #333; display: block; margin-top: 8px;">Team 2 Chunein:</label>
                    <select id="ptTeam2Select">
                        <option value="">Pehle Table Chunein</option>
                    </select>
                    <input type="text" id="ptTeam2ScoreInput" placeholder="Team 2 Score (jaise: 175/6 in 20 ov)">
                    
                    <input type="text" id="ptCustomNrrInput" placeholder="Optional: Team 1 NRR adjust (e.g. +1.245)" style="margin-top:8px;">

                    <button type="button" class="action-btn" id="savePointTableMatchBtn" style="margin-top: 10px; background: var(--primary-dark);">Points Table Match Update Karein</button>
                </div>

                <!-- NEW TABLE WRAPPER -->
                <div id="newTableSection" class="hidden">
                    <input type="text" id="newPtTableName" placeholder="Nayi Table/Group Name (jaise: IPL 2027 Points Table)" style="margin-top:2px;">
                    <input type="text" id="newPtTableTeams" placeholder="Teams comma se dalein (jaise: MI, CSK, RCB, KKR)" style="margin-top:6px;">
                    <button type="button" class="action-btn" id="createPtTableBtn" style="background: #009688; margin-top:6px;">Nayi Points Table Create Karein</button>
                </div>

                <div id="adminPtTablesList" style="margin-top: 10px;"></div>
            </div>
            
            <button type="button" id="closeAdminBtn" class="logout-btn" style="background: #757575;">Admin Panel Band Karein</button>
        </div>

        <button type="button" class="logout-btn" id="logoutBtn">Logout</button>
    </div>
</div>

<script>
    // 🔥 APNA FIREBASE CONFIGURATION YAHAN DAALEIN
    const firebaseConfig = {
        apiKey: "YOUR_API_KEY",
        authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
        databaseURL: "https://YOUR_PROJECT_ID-default-rtdb.firebaseio.com",
        projectId: "YOUR_PROJECT_ID",
        storageBucket: "YOUR_PROJECT_ID.appspot.com",
        messagingSenderId: "YOUR_SENDER_ID",
        appId: "YOUR_APP_ID"
    };

    // Initialize Firebase
    if (!firebase.apps.length) {
        firebase.initializeApp(firebaseConfig);
    }
    const db = firebase.database();

    const ADMIN_NUMBER = "9569981484";
    let isAdminUnlocked = false;

    function handleLogin() {
        let phoneInput = document.getElementById('userPhoneInput');
        if (!phoneInput) return;
        let phone = phoneInput.value.trim();
        
        if(phone.length !== 10) {
            alert("Kripya sahi 10-digit ka mobile number dalein!");
            return;
        }

        let myDeviceId = localStorage.getItem('my_device_id');
        if(!myDeviceId) {
            myDeviceId = 'dev_' + Math.random().toString(36).substring(2);
            localStorage.setItem('my_device_id', myDeviceId);
        }

        db.ref('active_sessions/' + phone).once('value', (snapshot) => {
            let currentActiveDevice = snapshot.val();

            if(currentActiveDevice && currentActiveDevice !== myDeviceId) {
                alert("Error: Ye number pehle se hi kisi doosri device par logged in hai!");
                return;
            }

            db.ref('active_sessions/' + phone).set(myDeviceId).catch(err => console.log(err));

            db.ref('users/' + phone).once('value', (userSnap) => {
                if(!userSnap.exists()) {
                    let initialPoints = (phone === ADMIN_NUMBER ? 15000 : 15);
                    db.ref('users/' + phone).set({ points: initialPoints, subscription: null, subExpiry: 0 });
                }
                localStorage.setItem('current_user', phone);
                loadDashboard();
            }).catch((error) => {
                console.error("Login Error:", error);
                localStorage.setItem('current_user', phone);
                loadDashboard();
            });
        }).catch((error) => {
            console.error("Session Error:", error);
            localStorage.setItem('current_user', phone);
            loadDashboard();
        });
    }

    function loadDashboard() {
        let currentPhone = localStorage.getItem('current_user');
        if(!currentPhone) return;

        document.getElementById('loginSection').classList.add('hidden');
        document.getElementById('dashboardSection').classList.remove('hidden');
        document.getElementById('appTabs').classList.remove('hidden');

        db.ref('users/' + currentPhone).on('value', (snapshot) => {
            let userData = snapshot.val();
            if(userData) {
                document.getElementById('userPoints').innerText = userData.points || 0;
                updateSubscriptionStatusUI(userData);
            }
        });

        setupRealtimeListeners();
    }

    function setupRealtimeListeners() {
        db.ref('matches').on('value', (snapshot) => { renderSchedule(snapshot.val() || {}); renderAdminMatchesList(snapshot.val() || {}); updateMatchDropdown(snapshot.val() || {}); });
        db.ref('points_tables').on('value', (snapshot) => { renderAllPointsTables(snapshot.val() || {}); populateAdminGroupDropdown(snapshot.val() || {}); renderAdminPointsTablesList(snapshot.val() || {}); });
        db.ref('groups').on('value', (snapshot) => { renderGroups(snapshot.val() || {}); renderAdminGroupsList(snapshot.val() || {}); });
    }

    function switchTab(tabName, btnElement) {
        document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
        document.querySelectorAll('.tab-btn').forEach(el => el.classList.remove('active'));
        document.getElementById('adminPanelSection').classList.add('hidden');

        if(tabName === 'matches') {
            document.getElementById('tabMatches').classList.remove('hidden');
        } else if(tabName === 'points') {
            document.getElementById('tabPoints').classList.remove('hidden');
        } else if(tabName === 'groups') {
            document.getElementById('tabGroups').classList.remove('hidden');
        } else if(tabName === 'wallet') {
            document.getElementById('tabWallet').classList.remove('hidden');
        }
        if(btnElement) btnElement.classList.add('active');
    }

    function checkAdminAccess() {
        let currentPhone = localStorage.getItem('current_user');
        if(!currentPhone) {
            alert("Pehle login karein!");
            return;
        }

        db.ref('users/' + currentPhone).once('value', (snapshot) => {
            let userData = snapshot.val();
            let currentTime = Date.now();
            let hasActiveSub = false;

            if(userData && userData.subscription && userData.subExpiry && userData.subExpiry > currentTime) {
                hasActiveSub = true;
            }

            if(!hasActiveSub && currentPhone !== ADMIN_NUMBER) {
                alert("Bina active subscription ke admin panel open nahi ho sakta!");
                return;
            }

            isAdminUnlocked = true;
            document.getElementById('adminPanelSection').classList.remove('hidden');
            
            if(currentPhone === ADMIN_NUMBER) {
                document.getElementById('mainAdminCodeBox').classList.remove('hidden');
            } else {
                document.getElementById('mainAdminCodeBox').classList.add('hidden');
            }
            alert("Admin Panel Successfully Unlocked!");
        });
    }

    function closeAdminPanel() {
        isAdminUnlocked = false;
        document.getElementById('adminPanelSection').classList.add('hidden');
    }

    function sendPointsToUserAction() {
        let currentPhone = localStorage.getItem('current_user');
        if(currentPhone !== ADMIN_NUMBER) {
            alert("Sirf Main Admin hi points bhej sakta hai!");
            return;
        }

        let targetPhone = document.getElementById('adminTargetPhone').value.trim();
        let pointsToAdd = parseInt(document.getElementById('adminSendPoints').value);

        if(targetPhone.length !== 10) {
            alert("Kripya user ka sahi 10-digit mobile number dalein!");
            return;
        }
        if(isNaN(pointsToAdd) || pointsToAdd <= 0) {
            alert("Kripya points ki sahi matra dalein!");
            return;
        }

        let userRef = db.ref('users/' + targetPhone);
        userRef.once('value', (snapshot) => {
            let currentPoints = 0;
            let subData = null;
            let subExp = 0;

            if(snapshot.exists()) {
                let val = snapshot.val();
                currentPoints = parseInt(val.points) || 0;
                subData = val.subscription || null;
                subExp = val.subExpiry || 0;
            }

            let updatedPoints = currentPoints + pointsToAdd;
            
            userRef.set({
                points: updatedPoints,
                subscription: subData,
                subExpiry: subExp
            }, (error) => {
                if(!error) {
                    document.getElementById('adminTargetPhone').value = "";
                    document.getElementById('adminSendPoints').value = "";
                    alert(`Successfully ${pointsToAdd} points user (${targetPhone}) ke wallet mein add kar diye gaye hain!`);
                } else {
                    alert("Points bhejne mein error aayi hai. Dobara koshish karein.");
                }
            });
        });
    }

    function createGroupAction() {
        let groupName = document.getElementById('groupNameInput').value.trim();
        let teamsInput = document.getElementById('groupTeamsInput').value.trim();

        if(!groupName || !teamsInput) {
            alert("Kripya Group Name aur Teams ke naam dalein!");
            return;
        }

        let teamsArr = teamsInput.split(',').map(t => t.trim()).filter(t => t.length > 0);
        db.ref('groups/' + groupName).set(teamsArr);

        document.getElementById('groupNameInput').value = "";
        document.getElementById('groupTeamsInput').value = "";
        alert(`Group '${groupName}' successfully ban gaya hai!`);
    }

    function buySubscription(cost, planName, extraDays, extraMins) {
        let currentPhone = localStorage.getItem('current_user');
        db.ref('users/' + currentPhone).once('value', (snapshot) => {
            let userData = snapshot.val();
            if(!userData || (userData.points || 0) < cost) {
                alert("Aapke paas sufficient points nahi hain!");
                return;
            }

            userData.points -= cost;
            let currentTime = Date.now();
            userData.subscription = planName;

            if(extraDays) userData.subExpiry = currentTime + (extraDays * 24 * 60 * 60 * 1000);
            else if(extraMins) userData.subExpiry = currentTime + (extraMins * 60 * 1000);

            db.ref('users/' + currentPhone).set(userData);

            db.ref('users/' + ADMIN_NUMBER).once('value', (adminSnap) => {
                let adminData = adminSnap.val() || { points: 15000 };
                adminData.points = (adminData.points || 0) + cost;
                db.ref('users/' + ADMIN_NUMBER).set(adminData);
            });

            alert(`Aapne '${planName}' successfully buy kar liya hai!`);
        });
    }

    function updateSubscriptionStatusUI(userData) {
        let subStatusElem = document.getElementById('subStatus');
        if(!userData || !userData.subscription) {
            subStatusElem.innerText = "Status: No Active Subscription";
            return;
        }
        let currentTime = Date.now();
        if(userData.subExpiry && userData.subExpiry > currentTime) {
            subStatusElem.innerText = `Active Plan: ${userData.subscription}`;
        } else {
            subStatusElem.innerText = "Status: Subscription Expired";
        }
    }

    function saveMatch() {
        let editId = document.getElementById('editMatchId').value;
        let series = document.getElementById('seriesName').value.trim();
        let seriesType = document.getElementById('seriesTypeOption').value;
        let format = document.getElementById('matchFormat').value.trim();
        let t1 = document.getElementById('team1').value.trim();
        let t2 = document.getElementById('team2').value.trim();
        let venue = document.getElementById('venue').value.trim();
        let toss = document.getElementById('matchTossUpdate').value.trim();

        if(!series || !format || !t1 || !t2) {
            alert("Kripya Series, Format, aur Dono Teams ke naam bharein!");
            return;
        }

        let matchesRef = db.ref('matches');
        if(editId !== "") {
            matchesRef.child(editId).update({ series, seriesType, format, t1, t2, venue, toss });
            document.getElementById('editMatchId').value = "";
            document.getElementById('saveMatchBtn').innerText = "Match Save Karein";
        } else {
            let newMatchRef = matchesRef.push();
            newMatchRef.set({ series, seriesType, format, t1, t2, venue, toss, winner: "", t1Score: "", t2Score: "" });
        }

        document.getElementById('seriesName').value = "";
        document.getElementById('matchFormat').value = "";
        document.getElementById('team1').value = "";
        document.getElementById('team2').value = "";
        document.getElementById('venue').value = "";
        document.getElementById('matchTossUpdate').value = "";
        alert("Match successfully save ho gaya hai!");
    }

    window.editMatch = function(id, m) {
        document.getElementById('editMatchId').value = id;
        document.getElementById('seriesName').value = m.series || "";
        document.getElementById('seriesTypeOption').value = m.seriesType || "same";
        document.getElementById('matchFormat').value = m.format || "";
        document.getElementById('team1').value = m.t1 || "";
        document.getElementById('team2').value = m.t2 || "";
        document.getElementById('venue').value = m.venue || "";
        document.getElementById('matchTossUpdate').value = m.toss || "";
        document.getElementById('saveMatchBtn').innerText = "Match Update Karein";
    };

    window.deleteMatch = function(id) {
        if(confirm("Kya aap is match ko delete karna chahte hain?")) {
            db.ref('matches/' + id).remove();
        }
    };

    function updateMatchResultAction() {
        let matchId = document.getElementById('matchSelectForUpdate').value;
        let winner = document.getElementById('matchWinner').value.trim();
        let t1Score = document.getElementById('team1ScoreDetails').value.trim();
        let t2Score = document.getElementById('team2ScoreDetails').value.trim();

        if(!matchId) {
            alert("Kripya result ke liye match chunein!");
            return;
        }

        db.ref('matches/' + matchId).update({
            winner: winner,
            t1Score: t1Score,
            t2Score: t2Score
        }, (error) => {
            if(!error) {
                document.getElementById('matchWinner').value = "";
                document.getElementById('team1ScoreDetails').value = "";
                document.getElementById('team2ScoreDetails').value = "";
                alert("Match ka result aur scores successfully update ho gaye hain!");
            }
        });
    }

    function renderAdminMatchesList(matchesObj) {
        let container = document.getElementById('adminMatchesList');
        let keys = Object.keys(matchesObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:11px; color:#666; margin:0;'>Koi match available nahi hai.</p>";
            return;
        }
        let html = "";
        keys.forEach((id) => {
            let m = matchesObj[id];
            let mJson = JSON.stringify(m).replace(/"/g, '&quot;');
            html += `<div style="display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid #eee; padding:4px 0; font-size:12px;">
                <span><b>${m.t1} vs ${m.t2}</b> (${m.format})</span>
                <div>
                    <button type="button" onclick='editMatch("${id}", ${mJson})' style="background:#ff9800; color:white; border:none; padding:2px 6px; border-radius:3px; cursor:pointer; font-size:10px; width:auto; margin-right:4px;">Edit</button>
                    <button type="button" onclick="deleteMatch('${id}')" style="background:#dc3545; color:white; border:none; padding:2px 6px; border-radius:3px; cursor:pointer; font-size:10px; width:auto;">Delete</button>
                </div>
            </div>`;
        });
        container.innerHTML = html;
    }

    function updateMatchDropdown(matchesObj) {
        let select = document.getElementById('matchSelectForUpdate');
        if(!select) return;
        select.innerHTML = "<option value=''>-- Match Chunein Result ke liye --</option>";
        Object.keys(matchesObj).forEach((id) => {
            let m = matchesObj[id];
            select.innerHTML += `<option value="${id}">${m.t1} vs ${m.t2} (${m.format})</option>`;
        });
    }

    function handleTableActionMode() {
        let mode = document.getElementById('tableActionMode').value;
        if(mode === 'existing') {
            document.getElementById('existingTableSection').classList.remove('hidden');
            document.getElementById('newTableSection').classList.add('hidden');
        } else {
            document.getElementById('existingTableSection').classList.add('hidden');
            document.getElementById('newTableSection').classList.remove('hidden');
        }
    }

    function createNewPointsTableDirectly() {
        let tableName = document.getElementById('newPtTableName').value.trim();
        let teamsInput = document.getElementById('newPtTableTeams').value.trim();

        if(!tableName || !teamsInput) {
            alert("Table Name aur Teams dono dalein!");
            return;
        }

        let teamsArr = teamsInput.split(',').map(t => t.trim()).filter(t => t.length > 0);
        let tableData = {};
        teamsArr.forEach(t => {
            tableData[t] = { played: 0, won: 0, lost: 0, nr: 0, nrr: '0.000', points: 0 };
        });

        db.ref('points_tables/' + tableName).set(tableData);
        db.ref('groups/' + tableName).set(teamsArr);

        document.getElementById('newPtTableName').value = "";
        document.getElementById('newPtTableTeams').value = "";
        alert("Nayi Points Table successfully create ho gayi!");
    }

    function addTeamToExistingTable() {
        let tableName = document.getElementById('ptGroupSelect').value.trim();
        let teamName = document.getElementById('extraTeamInput').value.trim();

        if(!tableName || !teamName) {
            alert("Table aur team ka naam dalein!");
            return;
        }

        db.ref(`points_tables/${tableName}/${teamName}`).set({ played: 0, won: 0, lost: 0, nr: 0, nrr: '0.000', points: 0 }, (error) => {
            if(!error) {
                document.getElementById('extraTeamInput').value = "";
                alert(`Team '${teamName}' ko table '${tableName}' mein jodh diya gaya hai!`);
            }
        });
    }

    function populateAdminGroupDropdown(tablesObj) {
        let groupSelect = document.getElementById('ptGroupSelect');
        let currentSelected = groupSelect.value;
        groupSelect.innerHTML = "<option value=''>-- Table Chunein --</option>";
        
        Object.keys(tablesObj).forEach(gName => {
            groupSelect.innerHTML += `<option value="${gName}">${gName}</option>`;
        });
        if(currentSelected) groupSelect.value = currentSelected;
    }

    function onAdminGroupSelected() {
        let groupName = document.getElementById('ptGroupSelect').value;
        let t1Select = document.getElementById('ptTeam1Select');
        let t2Select = document.getElementById('ptTeam2Select');
        
        if(!groupName) {
            t1Select.innerHTML = "<option value=''>Pehle Table Chunein</option>";
            t2Select.innerHTML = "<option value=''>Pehle Table Chunein</option>";
            return;
        }

        db.ref('points_tables/' + groupName).once('value', (snapshot) => {
            let teamsObj = snapshot.val() || {};
            let teams = Object.keys(teamsObj);
            let optionsHtml = "<option value=''>-- Team Chunein --</option>";
            teams.forEach(team => {
                optionsHtml += `<option value="${team}">${team}</option>`;
            });
            t1Select.innerHTML = optionsHtml;
            t2Select.innerHTML = optionsHtml;
        });
    }

    function savePointTableMatch() {
        let tableName = document.getElementById('ptGroupSelect').value.trim();
        let team1 = document.getElementById('ptTeam1Select').value.trim();
        let team2 = document.getElementById('ptTeam2Select').value.trim();
        let customNrr = document.getElementById('ptCustomNrrInput').value.trim();

        if(!tableName || !team1 || !team2 || team1 === team2) {
            alert("Sahi Table aur alag-alag Teams chunein!");
            return;
        }

        db.ref(`points_tables/${tableName}`).once('value', (snapshot) => {
            let tables = snapshot.val() || {};
            if(!tables[team1]) tables[team1] = { played: 0, won: 0, lost: 0, nr: 0, nrr: '0.000', points: 0 };
            if(!tables[team2]) tables[team2] = { played: 0, won: 0, lost: 0, nr: 0, nrr: '0.000', points: 0 };

            tables[team1].played = (parseInt(tables[team1].played) || 0) + 1;
            tables[team2].played = (parseInt(tables[team2].played) || 0) + 1;
            tables[team1].won = (parseInt(tables[team1].won) || 0) + 1;
            tables[team1].points = (parseInt(tables[team1].points) || 0) + 2;
            tables[team2].lost = (parseInt(tables[team2].lost) || 0) + 1;

            if(customNrr) tables[team1].nrr = customNrr;

            db.ref('points_tables/' + tableName).set(tables, () => {
                document.getElementById('ptTeam1ScoreInput').value = "";
                document.getElementById('ptTeam2ScoreInput').value = "";
                document.getElementById('ptCustomNrrInput').value = "";
                alert("Points Table match result ke mutabiq successfully update ho gayi hai!");
            });
        });
    }

    window.deleteEntireTable = function(tableName) {
        if(confirm(`Kya aap poori table '${tableName}' ko delete karna chahte hain?`)) {
            db.ref('points_tables/' + tableName).remove();
            db.ref('groups/' + tableName).remove();
            alert("Points table successfully delete kar di gayi hai!");
        }
    };

    function renderAdminPointsTablesList(tablesObj) {
        let container = document.getElementById('adminPtTablesList');
        let keys = Object.keys(tablesObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:12px; color:#666;'>Abhi koi points table nahi hai.</p>";
            return;
        }

        let html = "<div style='font-size:12px; font-weight:bold; margin-bottom:5px;'>Bani hui Tables (Delete Option):</div>";
        keys.forEach(tName => {
            let teamsCount = Object.keys(tablesObj[tName] || {}).length;
            html += `<div style="background:#fff; padding:5px 8px; border:1px solid #ccc; border-radius:4px; margin-bottom:4px; display:flex; justify-content:space-between; align-items:center;">
                <span><b>${tName}</b> (${teamsCount} Teams)</span>
                <button type="button" onclick="deleteEntireTable('${tName}')" style="background:#dc3545; color:white; border:none; padding:3px 6px; border-radius:3px; font-size:10px; cursor:pointer; width:auto;">Delete Table</button>
            </div>`;
        });
        container.innerHTML = html;
    }

    function renderAllPointsTables(tablesObj) {
        let container = document.getElementById('pointsTablesDisplayContainer');
        let keys = Object.keys(tablesObj);

        if(keys.length === 0) {
            container.innerHTML = `<div class="card"><h3>🏆 Points Table</h3><p style='font-size:13px; color:var(--text-muted); text-align:center;'>Abhi koi points table uplabdh nahi hai.</p></div>`;
            return;
        }

        let html = "";
        keys.forEach(tName => {
            let teamsObj = tablesObj[tName];
            let teamKeys = Object.keys(teamsObj);

            teamKeys.sort((a, b) => {
                let ptsDiff = (teamsObj[b].points || 0) - (teamsObj[a].points || 0);
                if(ptsDiff !== 0) return ptsDiff;
                return parseFloat(teamsObj[b].nrr || 0) - parseFloat(teamsObj[a].nrr || 0);
            });

            html += `<div class="pro-table-container" style="margin-bottom: 15px;">
                <div class="pro-table-header"><span>🏆</span> <span>${tName}</span></div>
                <table class="ptable">
                    <tr>
                        <th style="width: 35px;">#</th>
                        <th style="text-align:left;">Team</th>
                        <th>P</th><th>W</th><th>L</th><th>NR</th><th>Pts</th><th>NRR</th>
                    </tr>`;
            
            let rank = 1;
            teamKeys.forEach(t => {
                let d = teamsObj[t];
                let shortLogo = t.length >= 2 ? t.substring(0, 3).toUpperCase() : t.toUpperCase();
                html += `<tr>
                    <td>${rank++}</td>
                    <td>
                        <div class="team-badge-row">
                            <div class="team-mini-logo">${shortLogo}</div>
                            <span>${t}</span>
                        </div>
                    </td>
                    <td>${d.played || 0}</td><td>${d.won || 0}</td><td>${d.lost || 0}</td><td>${d.nr || 0}</td><td><b>${d.points || 0}</b></td><td>${d.nrr || '0.000'}</td>
                </tr>`;
            });
            html += `</table></div>`;
        });
        container.innerHTML = html;
    }

    function renderSchedule(matchesObj) {
        let container = document.getElementById('scheduleList');
        let keys = Object.keys(matchesObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:13px; color:var(--text-muted); text-align:center;'>Abhi koi match schedule nahi hai.</p>";
            return;
        }
        let html = "";
        keys.forEach((id) => {
            let m = matchesObj[id];
            let bannerHtml = "";
            if(m.winner) bannerHtml = `<div class="match-result-banner">🏆 ${m.winner}</div>`;
            else if(m.toss) bannerHtml = `<div class="match-toss-banner">📢 ${m.toss}</div>`;

            let scoresHtml = "";
            if(m.t1Score || m.t2Score) {
                scoresHtml = `<div style="font-size:12px; color:#444; margin-top:4px;">${m.t1}: <b>${m.t1Score || 'Yet to bat'}</b><br>${m.t2}: <b>${m.t2Score || 'Yet to bat'}</b></div>`;
            }

            html += `<div class="match-box">
                <div class="match-info-top"><span>${m.format}</span><span>📍 ${m.venue || 'N/A'}</span></div>
                <div class="match-teams"><span>🏏 ${m.t1}</span> vs <span>${m.t2} 🏏</span></div>
                ${scoresHtml} ${bannerHtml}
            </div>`;
        });
        container.innerHTML = html;
    }

    function renderGroups(groupsObj) {
        let container = document.getElementById('groupsContainer');
        let keys = Object.keys(groupsObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:13px; color:var(--text-muted);'>Abhi koi group nahi hai.</p>";
            return;
        }
        let html = "";
        keys.forEach(g => {
            html += `<div style="background:#f9f9f9; border:1px solid #ddd; padding:8px; border-radius:6px; margin-bottom:8px;"><strong>📌 ${g}</strong><br><span style="font-size:13px;">Teams: ${groupsObj[g].join(', ')}</span></div>`;
        });
        container.innerHTML = html;
    }

    function renderAdminGroupsList(groupsObj) {
        let container = document.getElementById('adminGroupsList');
        let keys = Object.keys(groupsObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:12px; color:#666;'>Abhi koi group nahi hai.</p>";
            return;
        }
        let html = "<div style='font-size:12px; font-weight:bold;'>Groups List:</div>";
        keys.forEach(g => {
            html += `<div style="background:#fff; padding:5px; border:1px solid #ccc; margin-top:3px; border-radius:4px; font-size:13px;"><b>${g}</b></div>`;
        });
        container.innerHTML = html;
    }

    function logoutUser() {
        isAdminUnlocked = false;
        localStorage.removeItem('current_user');
        document.getElementById('dashboardSection').classList.add('hidden');
        document.getElementById('appTabs').classList.add('hidden');
        document.getElementById('loginSection').classList.remove('hidden');
        document.getElementById('userPhoneInput').value = "";
    }

    document.addEventListener('DOMContentLoaded', function() {
        document.getElementById('adminBtn').addEventListener('click', checkAdminAccess);
        document.getElementById('loginBtn').addEventListener('click', handleLogin);
        document.getElementById('saveMatchBtn').addEventListener('click', saveMatch);
        document.getElementById('updateResultBtn').addEventListener('click', updateMatchResultAction);
        
        document.getElementById('sendPointsToUserBtn').addEventListener('click', sendPointsToUserAction);
        document.getElementById('createGroupBtn').addEventListener('click', createGroupAction);

        document.getElementById('createPtTableBtn').addEventListener('click', createNewPointsTableDirectly);
        document.getElementById('addTeamToTableBtn').addEventListener('click', addTeamToExistingTable);
        document.getElementById('savePointTableMatchBtn').addEventListener('click', savePointTableMatch);
        
        document.getElementById('tableActionMode').addEventListener('change', handleTableActionMode);
        document.getElementById('closeAdminBtn').addEventListener('click', closeAdminPanel);
        document.getElementById('logoutBtn').addEventListener('click', logoutUser);
        
        document.getElementById('ptGroupSelect').addEventListener('change', onAdminGroupSelected);

        document.querySelectorAll('.tab-btn').forEach(btn => {
            btn.addEventListener('click', function() { switchTab(this.getAttribute('data-tab'), this); });
        });

        document.querySelectorAll('.buy-sub-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                let cost = parseInt(this.getAttribute('data-cost'));
                let plan = this.getAttribute('data-plan');
                let days = parseInt(this.getAttribute('data-days')) || 0;
                let mins = parseInt(this.getAttribute('data-time')) || 0;
                buySubscription(cost, plan, days, mins);
            });
        });

        if(localStorage.getItem('current_user')) {
            loadDashboard();
        }
    });
</script>
</body>
</html>
