import React, { useState, useEffect } from "react";

/* ================= NAME GENERATOR ================= */
const FIRST_NAMES = [
  "Wanjiru","Mwende","Achieng","Abdi","Kamau","Njoki","Otieno","Ibrahim","Grace","Mercy",
  "Juma","Omondi","Wanjala","Esther","Hassan","Amina","Mutiso","Chebet","Kibet","Njeri"
];

const SURNAMES = [
  "Mwangi","Ochieng","Odhiambo","Wambui","Aden","Ali","Kimani","Mutua","Okoth","Omondi",
  "Awuor","Kilonzo","Ndegwa","Otieno","Mugo","Wekesa","Cheruiyot","Langat","Koech"
];

function randomName() {
  return `${FIRST_NAMES[Math.floor(Math.random()*FIRST_NAMES.length)]} ${
    SURNAMES[Math.floor(Math.random()*SURNAMES.length)]
  }`;
}

/* ================= CONFIG ================= */
const TOTAL_MEMBERS = 8000;
const GROUPS = 5;
const MEMBERS_PER_GROUP = TOTAL_MEMBERS / GROUPS;
const CONTRIBUTION_AMOUNT = 5;
const PRIMARY = "#c9a227";

/* ================= GENERATE MEMBERS ================= */
function generateMembers(count){
  return Array.from({length:count},(_,i)=>({
    name: randomName(),
    contribution: 0,
    nextPayout:i+1
  }));
}

const initGroups = Array.from({length:GROUPS},(_,g)=>({
  id:g+1,
  members:generateMembers(MEMBERS_PER_GROUP),
  currentIndex:0
}));

/* ================= MAIN APP ================= */
export default function FastChamaApp(){
  const [step,setStep]=useState(1);
  const [fullName,setFullName]=useState("");
  const [phone,setPhone]=useState("");
  const [email,setEmail]=useState("");
  const [address,setAddress]=useState("");
  const [password,setPassword]=useState("");
  const [rePassword,setRePassword]=useState("");
  const [termsAccepted,setTermsAccepted]=useState(false);
  const [mpesaMessage,setMpesaMessage]=useState("");

  const [memberID,setMemberID]=useState("");
  const [groups,setGroups]=useState(initGroups);
  const [dateTime,setDateTime]=useState(new Date());
  const [onlineMembers,setOnlineMembers]=useState(TOTAL_MEMBERS);

  /* REGISTER */
  const handleRegister=()=>{
    if(!fullName||!phone||!email||!address||!password||!rePassword||!termsAccepted){
      alert("Fill all fields");
      return;
    }
    if(password!==rePassword){
      alert("Passwords mismatch");
      return;
    }
    setStep(2);
  };

  /* VERIFY */
  const handleMpesaSubmit=()=>{
    if(!mpesaMessage){
      alert("Paste message");
      return;
    }
    const id=`FC${Math.floor(1000+Math.random()*9000)}`;
    setMemberID(id);
    setStep(3);
  };

  /* SIMULATION */
  useEffect(()=>{
    if(step!==3)return;

    const interval=setInterval(()=>{
      setGroups(prev=>prev.map(g=>{
        const newMembers=g.members.map(m=>({...m,contribution:m.contribution+CONTRIBUTION_AMOUNT}));
        return {...g,members:newMembers,currentIndex:(g.currentIndex+1)%MEMBERS_PER_GROUP};
      }));
      setOnlineMembers(Math.floor(TOTAL_MEMBERS*(0.8+Math.random()*0.2)));
      setDateTime(new Date());
    },3000);

    return()=>clearInterval(interval);
  },[step]);

  return(
    <div style={{
      background:"#000",
      minHeight:"100vh",
      display:"flex",
      justifyContent:"center",
      alignItems:"center",
      fontFamily:"Arial"
    }}>
      <div style={{width:"100%",maxWidth:420,minHeight:"100vh",background:"#000",padding:15,color:"#fff"}}>

        {/* REGISTER */}
        {step===1&&(
          <div style={{display:"flex",justifyContent:"center",alignItems:"center",minHeight:"100vh"}}>
            <div style={{background:"#fff",color:"#000",padding:25,borderRadius:12,width:"100%"}}>
              <h2 style={{textAlign:"center"}}>FAST CHAMA</h2>

              <input placeholder="Full name" style={{display:"block",margin:"10px 0",padding:10,width:"100%"}} onChange={e=>setFullName(e.target.value)} />
              <input placeholder="Phone" style={{display:"block",margin:"10px 0",padding:10,width:"100%"}} onChange={e=>setPhone(e.target.value)} />
              <input placeholder="Email" style={{display:"block",margin:"10px 0",padding:10,width:"100%"}} onChange={e=>setEmail(e.target.value)} />
              <input placeholder="Address" style={{display:"block",margin:"10px 0",padding:10,width:"100%"}} onChange={e=>setAddress(e.target.value)} />
              <input type="password" placeholder="Password" style={{display:"block",margin:"10px 0",padding:10,width:"100%"}} onChange={e=>setPassword(e.target.value)} />
              <input type="password" placeholder="Re-type" style={{display:"block",margin:"10px 0",padding:10,width:"100%"}} onChange={e=>setRePassword(e.target.value)} />

              <div style={{margin:"10px 0"}}>
                <input type="checkbox" onChange={e=>setTermsAccepted(e.target.checked)} /> Accept terms
              </div>

              <button style={{width:"100%",padding:12,background:"#000",color:"#fff",border:"none"}} onClick={handleRegister}>
                Continue
              </button>
            </div>
          </div>
        )}

        {/* PAYMENT */}
        {step===2&&(
          <div style={{display:"flex",justifyContent:"center",alignItems:"center",minHeight:"100vh"}}>
            <div style={{background:"#fff",color:"#000",padding:25,borderRadius:12,width:"100%"}}>
              <h2 style={{textAlign:"center"}}>CONGRATULATIONS!</h2>

              <p style={{
                textAlign:"center",
                fontWeight:"bold",
                fontSize:16,
                lineHeight:"22px"
              }}>
                MAKE PAYMENT OF 50 TO 0707564058 TO SECURE YOUR DASHBOARD ID
              </p>

              <textarea placeholder="Paste M-Pesa message" style={{width:"100%",padding:12,marginTop:15}} onChange={e=>setMpesaMessage(e.target.value)} />

              <button style={{width:"100%",padding:14,marginTop:15,background:"#000",color:"#fff",border:"none"}} onClick={handleMpesaSubmit}>
                VERIFY PAYMENT
              </button>
            </div>
          </div>
        )}

        {/* DASHBOARD */}
        {step===3&&(
          <>
            <div style={{display:"flex",justifyContent:"space-between"}}>
              <div>{dateTime.toLocaleDateString()} {dateTime.toLocaleTimeString()}</div>
              <div>Members online: {onlineMembers}</div>
            </div>

            <h2>FAST CHAMA AUTO PAYOUT</h2>
            <p>Welcome {fullName} — ID: {memberID}</p>

            {groups.map(g=>(
              <div key={g.id} style={{background:"#111",margin:10,padding:10,borderRadius:8}}>
                <h3>Group {g.id}</h3>
                <p style={{color:PRIMARY}}>Next payout: {g.members[g.currentIndex].name}</p>

                <ul>
                  {g.members.slice(0,8).map((m,i)=>(
                    <li key={i}>{m.name} — KSh {m.contribution}</li>
                  ))}
                  <li>…and more</li>
                </ul>
              </div>
            ))}
          </>
        )}

      </div>
    </div>
  );
}
