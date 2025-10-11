# -EduChain-Certificates-Badges-SHM-Marketplace-final
EduChain is a Web3 education ecosystem prototype. It uses a simulated blockchain to issue immutable certificates, verifiable skill badges (NFTs), and an incentive token (SHM). A peer-to-peer marketplace allows users to buy and sell courses, creating a decentralized and trustworthy learning economy for all.

 LIVE- HOSTED LINK- https://4a83a369b619.ngrok-free.app/
 to try you have to add data and it will be recorded and immediately show on dashboard and for specific person it will show on Student Portfolio


 
STEP BY STEP CODE EXECUTION IN GOOGLE COLAB:


STEP 1: !pip install streamlit pyngrok python-dotenv web3 requests


STEP 2: import requests
import os

NFT_STORAGE_KEY = os.getenv("")

def upload_to_nft_storage(file_path):
    headers = {"Authorization": f"Bearer {}"}
    with open(file_path, "rb") as f:
        response = requests.post(
            "https://api.nft.storage/upload",
            headers=headers,
            data=f
        )
    if response.status_code == 200:
        return response.json()["value"]["cid"]
    else:
        return None
        
STEP 3: with open(".env", "w") as f:
    f.write("""RPC_URL=https://go.getblock.us/cfb133d0b8e946e984ae6cf5de46309c
PRIVATE_KEY=0xBFfe28E2376049837a522650Ab0E6323612A4270
NFT_STORAGE_KEY=fe433137.e5c2e4eed83a46cb851164ffa77c0ef2
NGROK_AUTH_TOKEN=32x25OJIx2KcSVrvih1gnIVtg1I_3UDoczcBeDJn6zhKgzA7c
CHAIN_ID=137
""")
STEP 4:with open(".env", "w") as f:
    f.write("""RPC_URL=https://go.getblock.us/cfb133d0b8e946e984ae6cf5de46309c
PRIVATE_KEY=
NFT_STORAGE_KEY=
NGROK_AUTH_TOKEN=
CHAIN_ID=137
""")

Step 4: app_code = '''
import streamlit as st
import os
from dotenv import load_dotenv
from web3 import Web3
import hashlib
import datetime
import uuid

# ================== Load Environment ==================
load_dotenv()
RPC_URL = os.getenv("RPC_URL", "https://rpc-mumbai.maticvigil.com")
PRIVATE_KEY = os.getenv("PRIVATE_KEY", "")
CHAIN_ID = int(os.getenv("CHAIN_ID", "80001"))

# ================== Web3 Connection ==================
w3 = Web3(Web3.HTTPProvider(RPC_URL))

# ================== Utils ==================
def now_iso(): return str(datetime.datetime.utcnow())
def short_id(): return str(uuid.uuid4())[:8]

def calculate_hash(block):
    block_string = f"{block['index']}{block['timestamp']}{block['certificate_data']}{block['previous_hash']}"
    return hashlib.sha256(block_string.encode()).hexdigest()

# ================== Genesis Block ==================
def create_genesis_block():
    genesis = {
        "index": 0,
        "timestamp": now_iso(),
        "certificate_data": {
            "student_name": "GENESIS",
            "course_name": "GENESIS",
            "issue_date": now_iso(),
            "certificate_type": "GENESIS",
            "unique_id": "0"
        },
        "previous_hash": "0",
        "hash": ""
    }
    genesis["hash"] = calculate_hash(genesis)
    return genesis

if "blockchain" not in st.session_state:
    st.session_state.blockchain = [create_genesis_block()]
if "badge_types" not in st.session_state:
    st.session_state.badge_types = {}
if "badge_balances" not in st.session_state:
    st.session_state.badge_balances = {}
if "shm_balances" not in st.session_state:
    st.session_state.shm_balances = {}
if "shm_total_supply" not in st.session_state:
    st.session_state.shm_total_supply = 0
if "marketplace" not in st.session_state:
    st.session_state.marketplace = []

# ================== Streamlit Config ==================
st.set_page_config(page_title="🎓 EduChain MVP", layout="wide")

st.markdown("""
    <style>
    body {
        background: linear-gradient(to right, #f8f9fa, #e9ecef);
        font-family: 'Segoe UI', sans-serif;
    }
    .title {
        text-align: center;
        font-size: 2.5rem;
        font-weight: bold;
        color: #2c3e50;
        text-shadow: 0px 0px 10px rgba(0,255,200,0.4);
        margin-bottom: 20px;
    }
    .card {
        background: white;
        border-radius: 16px;
        padding: 20px;
        margin: 15px 0;
        box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        transition: all 0.3s ease-in-out;
    }
    .card:hover {
        transform: translateY(-6px) scale(1.01);
        box-shadow: 0 8px 20px rgba(0,255,180,0.6);
    }
    .metric-box {
        background: linear-gradient(145deg, #ffffff, #f1f1f1);
        border-radius: 12px;
        padding: 20px;
        text-align: center;
        transition: 0.3s ease-in-out;
    }
    .metric-box:hover {
        box-shadow: 0 0 20px rgba(0,200,255,0.5);
        transform: scale(1.05);
    }
    </style>
""", unsafe_allow_html=True)

st.markdown("<div class='title'>📚 EduChain — Certificates · Badges · SHM · Marketplace</div>", unsafe_allow_html=True)

page = st.sidebar.radio("🔗 Navigation", [
    "Dashboard",
    "Issue Certificate",
    "Skill Badges",
    "SHM Token",
    "Marketplace",
    "Verify / Recruiter",
    "Student Portfolio"
])

# ================== Dashboard ==================
if page == "Dashboard":
    st.markdown("<div class='card'><h3>📊 Dashboard Overview</h3></div>", unsafe_allow_html=True)

    total_certs = len(st.session_state.blockchain) - 1
    total_badges = sum(st.session_state.badge_balances.values()) if st.session_state.badge_balances else 0
    total_shm = st.session_state.shm_total_supply

    col1, col2, col3 = st.columns(3)
    with col1: st.markdown(f"<div class='metric-box'><h4>Certificates</h4><p>{total_certs}</p></div>", unsafe_allow_html=True)
    with col2: st.markdown(f"<div class='metric-box'><h4>Badges</h4><p>{total_badges}</p></div>", unsafe_allow_html=True)
    with col3: st.markdown(f"<div class='metric-box'><h4>SHM Supply</h4><p>{total_shm}</p></div>", unsafe_allow_html=True)

    st.subheader("Recent Certificates")
    for b in st.session_state.blockchain[-5:][::-1]:
        cert_info = f"""
        <div class='card'>
            <b>ID:</b> {b['certificate_data']['unique_id']} <br>
            <b>Student:</b> {b['certificate_data']['student_name']} <br>
            <b>Type:</b> {b['certificate_data']['certificate_type']} <br>
            <b>Course:</b> {b['certificate_data']['course_name']}
        </div>
        """
        st.markdown(cert_info, unsafe_allow_html=True)

# ================== Issue Certificate ==================
elif page == "Issue Certificate":
    st.subheader("📝 Issue New Certificate")
    student_name = st.text_input("Student Name")
    course_name = st.text_input("Course Name")
    cert_type = st.selectbox("Certificate Type", ["Workshop", "Course Completion", "Hackathon"])
    if st.button("Issue Certificate ✅"):
        new_block = {
            "index": len(st.session_state.blockchain),
            "timestamp": now_iso(),
            "certificate_data": {
                "student_name": student_name,
                "course_name": course_name,
                "issue_date": now_iso(),
                "certificate_type": cert_type,
                "unique_id": short_id()
            },
            "previous_hash": st.session_state.blockchain[-1]["hash"],
            "hash": ""
        }
        new_block["hash"] = calculate_hash(new_block)
        st.session_state.blockchain.append(new_block)
        st.success("Certificate Issued!")

# ================== Skill Badges ==================
elif page == "Skill Badges":
    st.subheader("🏅 Manage Skill Badges")
    badge_name = st.text_input("Badge Name")
    if st.button("Add Badge Type"):
        st.session_state.badge_types[badge_name] = True
        st.success(f"Badge '{badge_name}' Added!")

    student = st.text_input("Student (wallet)")
    badge = st.selectbox("Select Badge", list(st.session_state.badge_types.keys()) or ["None"])
    if st.button("Award Badge"):
        st.session_state.badge_balances[student] = st.session_state.badge_balances.get(student, 0) + 1
        st.success(f"Awarded {badge} to {student}")

# ================== SHM Token ==================
elif page == "SHM Token":
    st.subheader("💰 Peer-learning Incentives with SHM")
    student = st.text_input("Student Wallet")
    amt = st.number_input("Amount", 1, 1000)
    if st.button("Mint SHM"):
        st.session_state.shm_balances[student] = st.session_state.shm_balances.get(student, 0) + amt
        st.session_state.shm_total_supply += amt
        st.success(f"Minted {amt} SHM to {student}")

# ================== Marketplace ==================
elif page == "Marketplace":
    st.subheader("🛒 Course Marketplace")
    cname = st.text_input("Course Name")
    price = st.number_input("Price in SHM", 1, 1000)
    if st.button("List Course"):
        st.session_state.marketplace.append({"course": cname, "price": price})
        st.success("Course Listed!")

    st.write("Available Courses")
    for item in st.session_state.marketplace:
        st.markdown(f"<div class='card'><b>{item['course']}</b> — {item['price']} SHM</div>", unsafe_allow_html=True)

# ================== Verify ==================
elif page == "Verify / Recruiter":
    st.subheader("🔍 Verify Certificate")
    uid = st.text_input("Enter Certificate ID")
    if st.button("Verify"):
        cert = next((b for b in st.session_state.blockchain if b['certificate_data']['unique_id'] == uid), None)
        if cert:
            st.markdown(f"<div class='card'>✅ Valid Certificate<br>{cert['certificate_data']}</div>", unsafe_allow_html=True)
        else:
            st.error("Certificate not found ❌")

# ================== Student Portfolio ==================
elif page == "Student Portfolio":
    st.subheader("👤 Student Portfolio")
    student = st.text_input("Enter Student Wallet")
    if st.button("Show Portfolio"):
        certs = [b for b in st.session_state.blockchain if b['certificate_data']['student_name'] == student]
        st.write("Certificates")
        for c in certs:
            st.markdown(f"<div class='card'>{c['certificate_data']}</div>", unsafe_allow_html=True)
        st.write("Badges:", st.session_state.badge_balances.get(student, 0))
        st.write("SHM Balance:", st.session_state.shm_balances.get(student, 0))
'''
with open("app.py", "w") as f:
    f.write(app_code)
print("Wrote extended app.py with certificates, badges, SHM, and marketplace.")

STEP 5:from pyngrok import ngrok
import os

# Load NGROK token
os.environ["NGROK_AUTH_TOKEN"] = "32x25OJIx2KcSVrvih1gnIVtg1I_3UDoczcBeDJn6zhKgzA7c"
ngrok.set_auth_token(os.environ["NGROK_AUTH_TOKEN"])

# Start Streamlit
public_url = ngrok.connect(8501)
print("🚀 Public URL:", public_url)
!streamlit run app.py --server.port 8501
