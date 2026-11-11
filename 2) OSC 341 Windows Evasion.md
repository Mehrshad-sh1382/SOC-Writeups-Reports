# OSC 341 – Windows Evasion
## Incident Report: RDP Brute-Force Compromise & Windows Defender Bypass

**Hostname:** Elenora  
**Internal IP:** 172.16.17.126  
**Date of Incident:** 12 September 2024  
**Severity:** High  
**Status:** Confirmed Compromise

---

## 1. Executive Summary

On 12 September 2024, the Windows host **Elenora** (172.16.17.126) was successfully compromised via Remote Desktop Protocol (RDP). Following eight failed logon attempts, an attacker from the malicious IP address **89.187.177.73** (reported 154 times on AbuseIPDB; hosted by Datacamp, New York, USA) gained interactive remote access (Logon Type 10 – RemoteInteractive).

Immediately after authentication, the attacker performed reconnaissance of security controls using PowerShell and then executed a publicly documented Windows Defender detection bypass (Exploit-DB 51802) leveraging `rundll32.exe` and VBScript/mshtml to launch `calc.exe` as a proof-of-concept.

---

## 2. Incident Details

| Field              | Value                                              |
|--------------------|----------------------------------------------------|
| Hostname           | Elenora                                            |
| Internal IP        | 172.16.17.126                                      |
| Date / Time        | 12 September 2024, ~08:06 – 08:09 (local time)     |
| Attacker IP        | 89.187.177.73 (Datacamp, New York, USA)            |
| Reputation         | 154 reports on AbuseIPDB (malicious)               |
| Access Method      | RDP (Logon Type 10 – RemoteInteractive)            |
| Alert Type         | Malware / Defense Evasion                          |
| EDR Action         | Not Detected                                       |
| Trigger Process    | C:\Windows\System32\rundll32.exe                   |

---

## 3. Attack Timeline

| Time     | Event ID | Description                                                                 |
|----------|----------|-----------------------------------------------------------------------------|
| 08:06:59 | 4625     | Failed logon (0xC000006A – username correct, password wrong) × 8            |
| 08:07:02 | 4624     | Successful RDP logon (Type 10) from 89.187.177.73                           |
| 08:08:49 | 4104     | PowerShell: `Get-Service -Name WinDefend`                                   |
| 08:08:56 | 4104     | PowerShell: `Get-MpPreference \| Select-Object -Property DisableRealtimeMonitoring` |
| 08:09:03 | 4104     | PowerShell: `Get-NetFirewallProfile`                                        |
| 08:09:03 | 4104     | Additional recon: running services, TCP connections, user accounts, `net user`, `whoami /all` |
| 08:09:35 | 4688     | `rundll32.exe` launched with Defender bypass PoC (LoL45 variant) executing `calc.exe` |

---

## 4. Technical Analysis

### Initial Access
The attacker performed a short password-guessing campaign against an RDP-exposed account. After eight failures, a successful interactive logon occurred from a known malicious IP.

### Discovery / Reconnaissance
Post-authentication PowerShell commands focused on security posture (Defender service, real-time protection, firewall) and situational awareness (users, network connections, privileges).

### Defense Evasion
The attacker executed a known `rundll32` + VBScript + mshtml technique (Exploit-DB 51802). This Living-off-the-Land technique abuses the trusted binary `rundll32.exe` to execute arbitrary VBScript via `mshtml.dll`. The “LoL45” path segment was used to bypass the then-existing Trojan:Win32/Powessere.G detection. Launching `calc.exe` confirmed the evasion worked.

**Trigger Command:**
```cmd
rundll32 vbscript:"\..\mshtml\..\LoL45\..\mshtml,RunHTMLApplication " + String(CreateObject("Wscript.Shell").Run("calc.exe"), 0)
```

---

## 5. MITRE ATT&CK Mapping

| Tactic              | Technique                                      | ID         |
|---------------------|------------------------------------------------|------------|
| Initial Access      | Valid Accounts                                 | T1078      |
| Initial Access      | External Remote Services                       | T1133      |
| Credential Access   | Brute Force                                    | T1110      |
| Execution           | PowerShell                                     | T1059.001  |
| Execution           | Windows Command Shell                          | T1059.003  |
| Defense Evasion     | Exploitation for Defense Evasion               | T1211      |
| Defense Evasion     | System Binary Proxy Execution: Rundll32        | T1218.011  |

---

## 6. Conclusion

This was a successful credential-based RDP compromise followed by reconnaissance and a deliberate Windows Defender evasion attempt using a public LOLBin technique. The execution of `calc.exe` via the bypass indicates the technique was effective at the time of the incident.

---

## 7. Recommendations

- Reset compromised account passwords and restrict RDP access (VPN / IP allow-list + NLA).
- Hunt for persistence and perform full forensic analysis.
- Enable and monitor PowerShell Script Block Logging (4104) and%PDF-1.4
%���� ReportLab Generated PDF document (opensource)
1 0 obj
<<
/F1 2 0 R /F2 3 0 R
>>
endobj
2 0 obj
<<
/BaseFont /Helvetica /Encoding /WinAnsiEncoding /Name /F1 /Subtype /Type1 /Type /Font
>>
endobj
3 0 obj
<<
/BaseFont /Helvetica-Bold /Encoding /WinAnsiEncoding /Name /F2 /Subtype /Type1 /Type /Font
>>
endobj
4 0 obj
<<
/Contents 10 0 R /MediaBox [ 0 0 612 792 ] /Parent 9 0 R /Resources <<
/Font 1 0 R /ProcSet [ /PDF /Text /ImageB /ImageC /ImageI ]
>> /Rotate 0 /Trans <<

>> 
  /Type /Page
>>
endobj
5 0 obj
<<
/Contents 11 0 R /MediaBox [ 0 0 612 792 ] /Parent 9 0 R /Resources <<
/Font 1 0 R /ProcSet [ /PDF /Text /ImageB /ImageC /ImageI ]
>> /Rotate 0 /Trans <<

>> 
  /Type /Page
>>
endobj
6 0 obj
<<
/Contents 12 0 R /MediaBox [ 0 0 612 792 ] /Parent 9 0 R /Resources <<
/Font 1 0 R /ProcSet [ /PDF /Text /ImageB /ImageC /ImageI ]
>> /Rotate 0 /Trans <<

>> 
  /Type /Page
>>
endobj
7 0 obj
<<
/PageMode /UseNone /Pages 9 0 R /Type /Catalog
>>
endobj
8 0 obj
<<
/Author (\(anonymous\)) /CreationDate (D:20261111213617+00'00') /Creator (\(unspecified\)) /Keywords () /ModDate (D:20261111213617+00'00') /Producer (ReportLab PDF Library - \(opensource\)) 
  /Subject (\(unspecified\)) /Title (\(anonymous\)) /Trapped /False
>>
endobj
9 0 obj
<<
/Count 3 /Kids [ 4 0 R 5 0 R 6 0 R ] /Type /Pages
>>
endobj
10 0 obj
<<
/Filter [ /ASCII85Decode /FlateDecode ] /Length 2469
>>
stream
Gau0E>BAQ-&q9SYfVabXYo!PTlTEjdh!.SnWfl'lS^@g=5r'CB:jK>*REkWqB)u=,8tokfZuH^&94BOrFr)53!]p;losZ"Dgb7XX,6:,>\H-/S-7jXuan"T?)cD-!fdLruU"MC+jVlO'B+rW?+c^DjB%WuUgQ65i:`68*US\<>;]r6m9rZW9Lo9L/I(`e&?nls%FZs+1%b%>2l$2d+K_]0Po6g??Q@H^@HPj1T8$@BXT-MZuc`0*CR4$EFd<3^@b5"GNTV<a\,7WHf*>OBA]/DiYC01ZE!e^;n#5:]\n_p66Hk$Z2g*IpM;@_U#ItDnsp*ksrL<")$^uUcWJVXhS2pHNUm>u4p%9'%caG7J.;5@@=,!LTMaCC>d!9M\fYMoR:NAI0E/;38("N6uPYu.2aW."7$Qt78U#,h74R`F)P]!C8+N(;221Y&I-#MaF2,@7F0p[jGbIK<IJ<7+W@8YfL#mN"H%n-f6<P9>2k.Bb")C<?O*%(7fhF@SVtZ'J<\.t^:1Z919IoH<-fO@4KP9SX--IL')F#+ND@)<T+f!tspMKb_$:E)N_=:<W;o>,bh6WQ(W0#=O3d2t#j3nB,1%7hJDN/j!W*hV"1d9[/_I-mf8>d9[\?P1l,'2X44H/`Y1IBqF-Ip;6.$Hbh6t!*^oiPSmgq>0121B]]'T'C8j/C$tj\9fI>7^4BWEk8IY/#'1iBN*!j\dUPsih6\1uNX''cIa,e@Ar4T^01*$s%:f'&K1?N.@NWNmi0\X)Cfh7CdSOaK$nT;`G$0,!9`9V>?ST>*>#t/^/.DGAj=(ilYk:%Cs&qU"Y&!6'qD`:\NXJTeN6Q?!a#Flb_KR3GcjaHPO9H4l!PQ$jc;QotNUQnD&B38IDmJSpc_B5I4RkT4/s<EInE]'D6//[f`btq35a\a<+ut?kW,"H%FH\2ER)0\'pdrH>I+rXWafqMlD@2?##B^ZnBnY"O@,o'l@2jRt/4XBX+Gu%*&tt%ta-2KZ"ds=:`%_YJPf8++--`e[P8_ReChG2oZtpDck])oE:qCl!cr6OK#2&WJeHg%i4U[#X3P[c!\1`9HO0T0q)IHuUi-Q5oo'HVt>gCFN=JeB`.D9To6o:++FWB<X@$&)Uo!h5L23d;W+(isA\?M:Y''-%A3"pY1/7*Q<C9VNK+b9]t8er"IYfiXnTb,>RnFHoOjQi*F=H8DWH%d=f&?L-lkO[GgWE#I9gP$imL?!WbLoXHr.?mPnD5CSe'o<L'_pG%pqK/0l1=G(J(Od"`ZHlDEP!pO)!^`3nr_(G\VG<b;IYYX:L7AOS)<,Q;cGp\u+I'TKH:P"%Etqmg=d]Ar`g"7MA9*N>\RZ!(?6Q%KBmJirH-uG*9=9)@`AP`@D5<_g*=C;8hI/@_c`)q/WU7Qa@XmLDq>NkTO>fu@1sa\]O34Ar&Cgngfj]OQ'Jkht!%A"ITI[<=:T1"IPKgtFWhHn3VfhA&6,hb:i8M7'>,(dX0?(]20]tr`lhN-AmIt9AD=&(m]&n8[h9FdBS8)#=p/'M:J(M@$UV=.`?LjZ\:p0TWB35Ai&auLo%-dPjmZ+N5e93-n*qmBG?jfl%kj$Wn#k]5kgf84UqWd]Mhr4qZ9'9N1+g-#hiK;qVhrU9[^99rT8%GEP"i"c8)N:l^;\p\D;k8+9YPH&A(NHQsp!GEgTC4(#(IIFd_KR/!11g)M>^_id@H!e(ZAKCA^sgc[an\UAI;4$_T#7-!Nf<;!r(3h-)T`T[?@0WRj-l<+Yh`daj0?I>E<!'Eppbm1d:uYo^"$kk=HPmNWtV5Y2Yq@io>cjQGdd%MGR-#TlC%Eh>gjfLs5=0-d(\W\OWk<V'Z*1..p6)N2ng`tT$EQ/Ul`_r/[]Anh2n)5e8.Zi/Yrnjm3cUMX,SuQeYX7NC-rFlo\b0I,?,_=V77@BEW'p<7T/Mo*9W1Q2\A_NK49=-4(sG*\P)kKBj;iAe1s1o-81lOH0'XjdB)#qmT+//VO*Yk7++]Ve)d?4S)sBN8M7\/DWqpirX?Cq_)":#!AF!IgXY(c=spM:W)(Ii2+fujp/4'9WB(`aG-<^OeHEqhGTTbpAn61RETs7O[&J0OP<N_'S(B'hJc>%&QbVn!@U#(PBZ\3_4I7>GQu>9^=SF#U;d:O+/KRkm#3MT5l'_pEdgnr]UsmLHp<egYRTFIpUNTMlH@b,o'qQ_l5a^&4eEM\8H,pDt+(Xf\]TRSh1n.$nY<^Z/mGYWf"@)Bl\1"P8L/0S`E&T@d9J4+K8RIjd$\p,:;]j9QJ>nU`A!k!sAdJO&n//G,$]e=3eg69-PWaeLc<2QV:gZU1A3sV9>fs@$k]nDd4*QT.qe's9s2XPaB92/6iiKXdlt0h=[EAM`dd5tCJM]l[ZSj=t>BIi-g`m;PO>JuMnTSjDk#EVo+@F:JaDattn75S@E)=Xb]9M:4_")6'=3>>X/"BIhK/\HcLp5l<C/hkq3#16H&,fGg&-~>endstream
endobj
11 0 obj
<<
/Filter [ /ASCII85Decode /FlateDecode ] /Length 2504
>>
stream
Gau0D=``=U&:XAWf^V=!DC(%q-.754TqlRsf!X2n75Gs?Jg;TUaV(7Ws8+DKP*0Q#/t9dNN*-_(Mf3MFYjmM4s-"bEFU[`q'-Ie'1E`:N6$s3<q!@H*;L^]K+;jrUJ=e(:MO_c*YH>m(D"T:a"(qo$r%,ah!unlqKE;'))9ffZZS`EOXlY5%Y6?qi`-C597`>!<d]/3QQ:=l$_kC^uQZdIb],)$.LA6,7not#bYRNoLm)7R%[Wdf`+d(l:lQ0P/]Y/]rVGf*mT$\5q9(M<]4to!A@c`Ip%*$!$.a*&p+(+[5`ii(U2\.imQE`gulSglteF'4\s)%<EGMj.\>j&:T"=p[=,d/uVLQ"g'OO/dU,,\'DmqA.2]TNMq1]FP,*<.X]d>u.Cpih8'XKlQ/\rOb$)5OX^Op.>lR`bliZ5Li_XG"\g6%siEr!)P.TAQF@XD#^i-H?pQkE8H3Kj$hh1ee[r4kfmos$!&X(a5JH^^<EKoj+eV^'Z'/r,@D\[apRN_pZJ!$;X2h;5?bJG>#;IdY8D$q9!PfDF4dobOEu=p/=;*KMg.AWo!OL?YK<;WYkr%aT8Sh>dqrf`$S?32g#BD#!Q.,i!o)3Q#S!X)[D2"Cua"l[if;),3+J->[`;u#VHV`J<=\e1(tNd^)*J:L\LoLp?a98]iVLpX_aa1,1Npe15ngO2G'8q+k.$m7d'l6N`;H'hA=][cW+G17>MaJGhk7LiWTE6,u)+HcP<OOjlI,'Ob-]rV$,n4lb&_tjqVWa%L3;G(6m-$TQA73+b=7pGl'1&cUBJ98JCn8>g4P"A:2+Q0V^b49%I=j<jJiX3&[WeV8g#(>-Ubq_`3)eKVYmd?pEQ$l;)Aq&eV-CBj>7M$9U]<'U-oO""9f))<=@ePpIoa`QIMa^#"]T$AK]`^-L`<Kgf=5$/8=SGP;)B^m_+ai(KB>1I*3n8Q25M*-KZ7\9tPV=N0sIN[rn*OlS#n^;)gT<%0l'>R6cEq]^D\!A>W;h80NT0X;o4`^;B>J6$]A5[kk7S<'(Oc,upf!X)*/>gdF<&>Q%!7`T@s)6$#S!8To7XjIVq/eb+doWd&qF<LCB5uHdm'GG>#f&*_:Z!>V3r(nn"j!@o>ePKmj4CnTiR/5;d(75_3*tV[X['"1d9hUeG]:^@J!!uqhd$ML4dM=S`C9KpIOnffZTa8#Gb3"SX[Het!3!*RnDjI19'g0l&<IUS&>T/Q'<\;]PlYEalS8]8tE5.RJ.&^"Vd>A8FIU&llH,k?OpCI'*.V/j315@;LkR!&a;l9cZ1Q5KN9hi`^+5kK2VkiC-`8slJj>$n<'CObCEgi-,F)ZI84pc)(%n/?I\\JQ_Rm!:gp9c+5fqb\t7Z(\;*nF%f]G,7Oo'EoDpC2U56!k0G'sXindWiVbFa0f2b8`=^$9\Hp'Da0WG^i^-BbLb&:3SQ5'&+K<[4^T.(37h5=i5ht1Z>W=@bT"dHOEk4e__WQ;o+aIGA,/teb?20"i^1#YkaG2BZE8Jc)j0g*QQ^d!A;%T`NEZ&f#p+jY,T5#.kmkO,NVXHTn%sBr%lSMFn><%7Cb\Bbk_<<eq@^f\Q]4DPm0:.?jfH$#uS6WenVBU&eV"Q\\:R2HHPIteY/V]#5B"p0O]ZE=n?4EHVL5I;j1=j5[)I$4:t9TVBTo'1U^O60^`b)=N+&&e;a3t1REJQ7mF^BRI*mFg2d5M`dq2)Te1M74s!qZG\g&kf#A>,jSg5#3JS^1[d?YPWmC+h?tPYI[*dpjq'RE(dcpHF4Ta:Z\H2M2eO+[nm/qSm_&b`(^;k=WS*oHYdf_hkJrb-Gis;gI=GuNoGOs"=n<"L*<X\ar$(T\)'5lnjjJW7W33K7UeU;*oZk;Xpo[JU%btN!!<+fVXF<`[)L$D'Q@-H,GE+)KQAa\qGn)0F#0O.Ba"3tf9*ig>)3I'@nLf`u`\4KI$GrqUh@(J&,]046c[?[5'i]:95.=9m0Zn-Aj]'-n)?>jY6%Hq(#r/%Q3'^@^OY^dYf/eVW&%hDHs2>CGo5Ag7)m(?'mHYfLt.IZ$NhaP?E)*<)nW'.F4[Xq=?5+JuJ(KiI9mbuccg(I[./,8eqK+79($TLX`$23u1Ot]o'pKNX(7E0JL\s)l6H"="TEUUacE@:H/gq,6M@I.H;fZ]:E^lLNE=PMOLa1MfGbntR)nYjXS'':/uZKrA!*]X7NQRdHb;TXi]2>A8?V">f)G5-8iNG\)??]1f1K=@3lpaW.#Q%?O%k24Lc8*o/W@/nf_;HopF9CunEHra#q[E',k;kf8*\m-j7f^#-58S?CnT5]jfVf-F;%Rt_i_9$p.f4Hde..cVgNBN`W]Mu5B*f3YFVGltD,d_CYp,pnLajeic%,9b/J%_GS&]QqYX4?1\Eq@Gfq.BMlIJ]fYChbH&>,iZ$<*Zh12f,G&C8jtOf@e6+)Ii>G?go<5>,A0DZ-h+lR\tS?g?c>XKD;AcZYF[W^@%bZ#@@-ln*<+(#8ZJ#/4lm*E<BkR!$\uS[f~>endstream
endobj
12 0 obj
<<
/Filter [ /ASCII85Decode /FlateDecode ] /Length 1864
>>
stream
Gatm:gN)%,&:O:Sm%`!4N2R+1jirY<M5O,68t+n]0+\F^W'Pq75UlR,Scs8Cj2TPA7l.SA&:6EMcgI[[_^CFno8;c1K&6Km;VJ$;8a.oHdc-:)()L=2df#Z1$kK]'0Ze87c[52m>@$0%HF,S%P7]CHBPbgu@;Wfgd\Jhc%l2en;J38#,WD`b-C7]Qar2@l#h"nZhrM??^2,Xf:?%P3K^LIHQNbX:mjY_Gm;/W3Usa!p[s5XBOfBm6.+fXiF)-_2h@#^#6]8]nnI%[9j)-O79dc-R)P[k@JA4Ec[?U$K6jU)u!sOVH6m:fQ154P1\sB0LKlkghAQH<?2bU8n]d'co8rKO3FJh#Vle8Z2L:-C)Z1T72[8C9MRF%3/CYPrA:Z=Yse*4*U"II1^]lY2=</[<UU7eXXUfF5mSft+=kWG!bj<]Mfnt[I9!M4rpBHsl66r;ImRB3B2au[7:aBI>("XM3U,`4qY91%[_C;g$,UbnPLqP+l?Y#dr.KgYc3R6Z#@(gA_:F$5.iU_mC'ljN9l`Y;SeqLgQo-6<1UPt-"o_CI6jI^lj"2/nUXK<!StPAnqq*V@@blj/_/3S+^<@=S0D^6Z;Z<K9DEL%c0;=dJD`K<SEDTtoZW/J^SbR<\&O]_i7i<E(i#Bdlnf`kr%=mo_'&8N)sCd%%,?32V.6:BLCPP.1XbKWPGdUUW_kM!Sn>MD.59n=j"RNF/$uM=Ho82?epcLPIfa@d2sj(`T5mNJ:*\"PP=XYs;m\,0YiN8HKY&9iT9RdRXj[T^=u6H*6VmjIkremA88_V50`\j(q*i`61P2=8]FjHU$f.UjerdhAI[tK+9%se(1>pZ/\\>3&j+.NcrpE[LK)#,%kcSd!r"cOH.?qc#Xa!j3I%c+hZPO*H-*4R[8SYetd0.gjl??<[\X@qUo%R5[*%!S.-/EfgtWnm3'D,.)ab-bjOutQtRk5VdDeTB:[6oFKKrW(gJt6[16jp.#Hk]*b'nOk^8Zs0g*mTgdk.@%:%q6hMV23o!WP:TA+]f_/?k'28jpb\te`/mQYotdCWoa]L)OpTDm13J$:7"d+ENudZ"pZ5(OOF\FimD"(mA@eqZluXYo04)a7'u.r%A8BPe*`+Z3h?)jH*=L/P5%<\QB&Z0[NRM#;%`I+21`+.4lbm$[QtpcMp,+t@:Mr$/R-CCEAK*PTcAC'>/%l-#u/N2NMuSZ60oj"L`TR[Q_Y3X0QcE]AA_9]K:QW#925qH$c9Ni,*RJHsgURuG@l+oPJ7Y)cl_QHOLtR@G)W\1t]`dGABA](oM2ds4WThEroo9"S8H\NP%P2U(SCm1%ggl+TlV4uh42lU,<P9dVi2#sE'4kRP,OAL9u*h#)E*_Qbr!O!7a1<>[uT/<\S_4O"?XGruKTSr4Eg@VaUi<9uue5M5>eHA<T[F+s,Bh\b86a6^&$lVQiTi;_ise(n#0l\"\Ah@H-%Q,HPgAJ3JY-`-Ht!T:=kj9d$&KFcmp)<)e^aq,r&YIMiFVS\A2K=f(9!beuI!C3_4aDXRMEAl4WEM\2f)5NA_l+<D1::$k9_oAHUC$\>:/kR^CB@D<fd=]tC$El^(6GH1[,2XtMKe/Kd/W5S!1b122]Q"L$GR`V]CK<ZZn0a-%XEtiE)-0bJNQin1Qnr%#RQ&91BEQ\MarEr3'mA4QAL(f/g^oLhb;SAMUfVMFO]W?<>)6HggMs<beq2oh+aC8CMq;q5AM$[7SIEN93Bu6Dq.K`"chTCZm.HRPe[KUK->VoKmkcndg[7D;LFb/lCCi&Es6"lZ_"&K>?l^%HXK>LiUYLQa4$/??#dk%G45k^\k7nGaDIN,`cB>i2.m@iiMANo=KEpQQMkYN;iLu0Z!(ZtaZi~>endstream
endobj
xref
0 13
0000000000 65535 f 
0000000061 00000 n 
0000000102 00000 n 
0000000209 00000 n 
0000000321 00000 n 
0000000515 00000 n 
0000000709 00000 n 
0000000903 00000 n 
0000000971 00000 n 
0000001251 00000 n 
0000001322 00000 n 
0000003883 00000 n 
0000006479 00000 n 
trailer
<<
/ID 
[<b8cb41546beec6ef1a94fcc6ee7defac><b8cb41546beec6ef1a94fcc6ee7defac>]
% ReportLab generated PDF document -- digest (opensource)

/Info 8 0 R
/Root 7 0 R
/Size 13
>>
startxref
8435
%%EOF
 process creation with command lines (4688).
- Create detections for `rundll32` + `vbscript:` / `mshtml` / `RunHTMLApplication` patterns.
- Enforce account lockout policies.

---

## References
[1) SOC 321.pdf](https://github.com/user-attachments/files/34713650/1.SOC.321.pdf)

- [Exploit-DB 51802 – Microsoft Windows Defender VBScript Detection Bypass](https://www.exploit-db.com/exploits/51802)
- MITRE ATT&CK

---

*OSC 341 – Windows Evasion write-up*





%PDF-1.4
%���� ReportLab Generated PDF document (opensource)
1 0 obj
<<
/F1 2 0 R /F2 3 0 R
>>
endobj
2 0 obj
<<
/BaseFont /Helvetica /Encoding /WinAnsiEncoding /Name /F1 /Subtype /Type1 /Type /Font
>>
endobj
3 0 obj
<<
/BaseFont /Helvetica-Bold /Encoding /WinAnsiEncoding /Name /F2 /Subtype /Type1 /Type /Font
>>
endobj
4 0 obj
<<
/Contents 10 0 R /MediaBox [ 0 0 612 792 ] /Parent 9 0 R /Resources <<
/Font 1 0 R /ProcSet [ /PDF /Text /ImageB /ImageC /ImageI ]
>> /Rotate 0 /Trans <<

>> 
  /Type /Page
>>
endobj
5 0 obj
<<
/Contents 11 0 R /MediaBox [ 0 0 612 792 ] /Parent 9 0 R /Resources <<
/Font 1 0 R /ProcSet [ /PDF /Text /ImageB /ImageC /ImageI ]
>> /Rotate 0 /Trans <<

>> 
  /Type /Page
>>
endobj
6 0 obj
<<
/Contents 12 0 R /MediaBox [ 0 0 612 792 ] /Parent 9 0 R /Resources <<
/Font 1 0 R /ProcSet [ /PDF /Text /ImageB /ImageC /ImageI ]
>> /Rotate 0 /Trans <<

>> 
  /Type /Page
>>
endobj
7 0 obj
<<
/PageMode /UseNone /Pages 9 0 R /Type /Catalog
>>
endobj
8 0 obj
<<
/Author (\(anonymous\)) /CreationDate (D:20261111213617+00'00') /Creator (\(unspecified\)) /Keywords () /ModDate (D:20261111213617+00'00') /Producer (ReportLab PDF Library - \(opensource\)) 
  /Subject (\(unspecified\)) /Title (\(anonymous\)) /Trapped /False
>>
endobj
9 0 obj
<<
/Count 3 /Kids [ 4 0 R 5 0 R 6 0 R ] /Type /Pages
>>
endobj
10 0 obj
<<
/Filter [ /ASCII85Decode /FlateDecode ] /Length 2469
>>
stream
Gau0E>BAQ-&q9SYfVabXYo!PTlTEjdh!.SnWfl'lS^@g=5r'CB:jK>*REkWqB)u=,8tokfZuH^&94BOrFr)53!]p;losZ"Dgb7XX,6:,>\H-/S-7jXuan"T?)cD-!fdLruU"MC+jVlO'B+rW?+c^DjB%WuUgQ65i:`68*US\<>;]r6m9rZW9Lo9L/I(`e&?nls%FZs+1%b%>2l$2d+K_]0Po6g??Q@H^@HPj1T8$@BXT-MZuc`0*CR4$EFd<3^@b5"GNTV<a\,7WHf*>OBA]/DiYC01ZE!e^;n#5:]\n_p66Hk$Z2g*IpM;@_U#ItDnsp*ksrL<")$^uUcWJVXhS2pHNUm>u4p%9'%caG7J.;5@@=,!LTMaCC>d!9M\fYMoR:NAI0E/;38("N6uPYu.2aW."7$Qt78U#,h74R`F)P]!C8+N(;221Y&I-#MaF2,@7F0p[jGbIK<IJ<7+W@8YfL#mN"H%n-f6<P9>2k.Bb")C<?O*%(7fhF@SVtZ'J<\.t^:1Z919IoH<-fO@4KP9SX--IL')F#+ND@)<T+f!tspMKb_$:E)N_=:<W;o>,bh6WQ(W0#=O3d2t#j3nB,1%7hJDN/j!W*hV"1d9[/_I-mf8>d9[\?P1l,'2X44H/`Y1IBqF-Ip;6.$Hbh6t!*^oiPSmgq>0121B]]'T'C8j/C$tj\9fI>7^4BWEk8IY/#'1iBN*!j\dUPsih6\1uNX''cIa,e@Ar4T^01*$s%:f'&K1?N.@NWNmi0\X)Cfh7CdSOaK$nT;`G$0,!9`9V>?ST>*>#t/^/.DGAj=(ilYk:%Cs&qU"Y&!6'qD`:\NXJTeN6Q?!a#Flb_KR3GcjaHPO9H4l!PQ$jc;QotNUQnD&B38IDmJSpc_B5I4RkT4/s<EInE]'D6//[f`btq35a\a<+ut?kW,"H%FH\2ER)0\'pdrH>I+rXWafqMlD@2?##B^ZnBnY"O@,o'l@2jRt/4XBX+Gu%*&tt%ta-2KZ"ds=:`%_YJPf8++--`e[P8_ReChG2oZtpDck])oE:qCl!cr6OK#2&WJeHg%i4U[#X3P[c!\1`9HO0T0q)IHuUi-Q5oo'HVt>gCFN=JeB`.D9To6o:++FWB<X@$&)Uo!h5L23d;W+(isA\?M:Y''-%A3"pY1/7*Q<C9VNK+b9]t8er"IYfiXnTb,>RnFHoOjQi*F=H8DWH%d=f&?L-lkO[GgWE#I9gP$imL?!WbLoXHr.?mPnD5CSe'o<L'_pG%pqK/0l1=G(J(Od"`ZHlDEP!pO)!^`3nr_(G\VG<b;IYYX:L7AOS)<,Q;cGp\u+I'TKH:P"%Etqmg=d]Ar`g"7MA9*N>\RZ!(?6Q%KBmJirH-uG*9=9)@`AP`@D5<_g*=C;8hI/@_c`)q/WU7Qa@XmLDq>NkTO>fu@1sa\]O34Ar&Cgngfj]OQ'Jkht!%A"ITI[<=:T1"IPKgtFWhHn3VfhA&6,hb:i8M7'>,(dX0?(]20]tr`lhN-AmIt9AD=&(m]&n8[h9FdBS8)#=p/'M:J(M@$UV=.`?LjZ\:p0TWB35Ai&auLo%-dPjmZ+N5e93-n*qmBG?jfl%kj$Wn#k]5kgf84UqWd]Mhr4qZ9'9N1+g-#hiK;qVhrU9[^99rT8%GEP"i"c8)N:l^;\p\D;k8+9YPH&A(NHQsp!GEgTC4(#(IIFd_KR/!11g)M>^_id@H!e(ZAKCA^sgc[an\UAI;4$_T#7-!Nf<;!r(3h-)T`T[?@0WRj-l<+Yh`daj0?I>E<!'Eppbm1d:uYo^"$kk=HPmNWtV5Y2Yq@io>cjQGdd%MGR-#TlC%Eh>gjfLs5=0-d(\W\OWk<V'Z*1..p6)N2ng`tT$EQ/Ul`_r/[]Anh2n)5e8.Zi/Yrnjm3cUMX,SuQeYX7NC-rFlo\b0I,?,_=V77@BEW'p<7T/Mo*9W1Q2\A_NK49=-4(sG*\P)kKBj;iAe1s1o-81lOH0'XjdB)#qmT+//VO*Yk7++]Ve)d?4S)sBN8M7\/DWqpirX?Cq_)":#!AF!IgXY(c=spM:W)(Ii2+fujp/4'9WB(`aG-<^OeHEqhGTTbpAn61RETs7O[&J0OP<N_'S(B'hJc>%&QbVn!@U#(PBZ\3_4I7>GQu>9^=SF#U;d:O+/KRkm#3MT5l'_pEdgnr]UsmLHp<egYRTFIpUNTMlH@b,o'qQ_l5a^&4eEM\8H,pDt+(Xf\]TRSh1n.$nY<^Z/mGYWf"@)Bl\1"P8L/0S`E&T@d9J4+K8RIjd$\p,:;]j9QJ>nU`A!k!sAdJO&n//G,$]e=3eg69-PWaeLc<2QV:gZU1A3sV9>fs@$k]nDd4*QT.qe's9s2XPaB92/6iiKXdlt0h=[EAM`dd5tCJM]l[ZSj=t>BIi-g`m;PO>JuMnTSjDk#EVo+@F:JaDattn75S@E)=Xb]9M:4_")6'=3>>X/"BIhK/\HcLp5l<C/hkq3#16H&,fGg&-~>endstream
endobj
11 0 obj
<<
/Filter [ /ASCII85Decode /FlateDecode ] /Length 2504
>>
stream
Gau0D=``=U&:XAWf^V=!DC(%q-.754TqlRsf!X2n75Gs?Jg;TUaV(7Ws8+DKP*0Q#/t9dNN*-_(Mf3MFYjmM4s-"bEFU[`q'-Ie'1E`:N6$s3<q!@H*;L^]K+;jrUJ=e(:MO_c*YH>m(D"T:a"(qo$r%,ah!unlqKE;'))9ffZZS`EOXlY5%Y6?qi`-C597`>!<d]/3QQ:=l$_kC^uQZdIb],)$.LA6,7not#bYRNoLm)7R%[Wdf`+d(l:lQ0P/]Y/]rVGf*mT$\5q9(M<]4to!A@c`Ip%*$!$.a*&p+(+[5`ii(U2\.imQE`gulSglteF'4\s)%<EGMj.\>j&:T"=p[=,d/uVLQ"g'OO/dU,,\'DmqA.2]TNMq1]FP,*<.X]d>u.Cpih8'XKlQ/\rOb$)5OX^Op.>lR`bliZ5Li_XG"\g6%siEr!)P.TAQF@XD#^i-H?pQkE8H3Kj$hh1ee[r4kfmos$!&X(a5JH^^<EKoj+eV^'Z'/r,@D\[apRN_pZJ!$;X2h;5?bJG>#;IdY8D$q9!PfDF4dobOEu=p/=;*KMg.AWo!OL?YK<;WYkr%aT8Sh>dqrf`$S?32g#BD#!Q.,i!o)3Q#S!X)[D2"Cua"l[if;),3+J->[`;u#VHV`J<=\e1(tNd^)*J:L\LoLp?a98]iVLpX_aa1,1Npe15ngO2G'8q+k.$m7d'l6N`;H'hA=][cW+G17>MaJGhk7LiWTE6,u)+HcP<OOjlI,'Ob-]rV$,n4lb&_tjqVWa%L3;G(6m-$TQA73+b=7pGl'1&cUBJ98JCn8>g4P"A:2+Q0V^b49%I=j<jJiX3&[WeV8g#(>-Ubq_`3)eKVYmd?pEQ$l;)Aq&eV-CBj>7M$9U]<'U-oO""9f))<=@ePpIoa`QIMa^#"]T$AK]`^-L`<Kgf=5$/8=SGP;)B^m_+ai(KB>1I*3n8Q25M*-KZ7\9tPV=N0sIN[rn*OlS#n^;)gT<%0l'>R6cEq]^D\!A>W;h80NT0X;o4`^;B>J6$]A5[kk7S<'(Oc,upf!X)*/>gdF<&>Q%!7`T@s)6$#S!8To7XjIVq/eb+doWd&qF<LCB5uHdm'GG>#f&*_:Z!>V3r(nn"j!@o>ePKmj4CnTiR/5;d(75_3*tV[X['"1d9hUeG]:^@J!!uqhd$ML4dM=S`C9KpIOnffZTa8#Gb3"SX[Het!3!*RnDjI19'g0l&<IUS&>T/Q'<\;]PlYEalS8]8tE5.RJ.&^"Vd>A8FIU&llH,k?OpCI'*.V/j315@;LkR!&a;l9cZ1Q5KN9hi`^+5kK2VkiC-`8slJj>$n<'CObCEgi-,F)ZI84pc)(%n/?I\\JQ_Rm!:gp9c+5fqb\t7Z(\;*nF%f]G,7Oo'EoDpC2U56!k0G'sXindWiVbFa0f2b8`=^$9\Hp'Da0WG^i^-BbLb&:3SQ5'&+K<[4^T.(37h5=i5ht1Z>W=@bT"dHOEk4e__WQ;o+aIGA,/teb?20"i^1#YkaG2BZE8Jc)j0g*QQ^d!A;%T`NEZ&f#p+jY,T5#.kmkO,NVXHTn%sBr%lSMFn><%7Cb\Bbk_<<eq@^f\Q]4DPm0:.?jfH$#uS6WenVBU&eV"Q\\:R2HHPIteY/V]#5B"p0O]ZE=n?4EHVL5I;j1=j5[)I$4:t9TVBTo'1U^O60^`b)=N+&&e;a3t1REJQ7mF^BRI*mFg2d5M`dq2)Te1M74s!qZG\g&kf#A>,jSg5#3JS^1[d?YPWmC+h?tPYI[*dpjq'RE(dcpHF4Ta:Z\H2M2eO+[nm/qSm_&b`(^;k=WS*oHYdf_hkJrb-Gis;gI=GuNoGOs"=n<"L*<X\ar$(T\)'5lnjjJW7W33K7UeU;*oZk;Xpo[JU%btN!!<+fVXF<`[)L$D'Q@-H,GE+)KQAa\qGn)0F#0O.Ba"3tf9*ig>)3I'@nLf`u`\4KI$GrqUh@(J&,]046c[?[5'i]:95.=9m0Zn-Aj]'-n)?>jY6%Hq(#r/%Q3'^@^OY^dYf/eVW&%hDHs2>CGo5Ag7)m(?'mHYfLt.IZ$NhaP?E)*<)nW'.F4[Xq=?5+JuJ(KiI9mbuccg(I[./,8eqK+79($TLX`$23u1Ot]o'pKNX(7E0JL\s)l6H"="TEUUacE@:H/gq,6M@I.H;fZ]:E^lLNE=PMOLa1MfGbntR)nYjXS'':/uZKrA!*]X7NQRdHb;TXi]2>A8?V">f)G5-8iNG\)??]1f1K=@3lpaW.#Q%?O%k24Lc8*o/W@/nf_;HopF9CunEHra#q[E',k;kf8*\m-j7f^#-58S?CnT5]jfVf-F;%Rt_i_9$p.f4Hde..cVgNBN`W]Mu5B*f3YFVGltD,d_CYp,pnLajeic%,9b/J%_GS&]QqYX4?1\Eq@Gfq.BMlIJ]fYChbH&>,iZ$<*Zh12f,G&C8jtOf@e6+)Ii>G?go<5>,A0DZ-h+lR\tS?g?c>XKD;AcZYF[W^@%bZ#@@-ln*<+(#8ZJ#/4lm*E<BkR!$\uS[f~>endstream
endobj
12 0 obj
<<
/Filter [ /ASCII85Decode /FlateDecode ] /Length 1864
>>
stream
Gatm:gN)%,&:O:Sm%`!4N2R+1jirY<M5O,68t+n]0+\F^W'Pq75UlR,Scs8Cj2TPA7l.SA&:6EMcgI[[_^CFno8;c1K&6Km;VJ$;8a.oHdc-:)()L=2df#Z1$kK]'0Ze87c[52m>@$0%HF,S%P7]CHBPbgu@;Wfgd\Jhc%l2en;J38#,WD`b-C7]Qar2@l#h"nZhrM??^2,Xf:?%P3K^LIHQNbX:mjY_Gm;/W3Usa!p[s5XBOfBm6.+fXiF)-_2h@#^#6]8]nnI%[9j)-O79dc-R)P[k@JA4Ec[?U$K6jU)u!sOVH6m:fQ154P1\sB0LKlkghAQH<?2bU8n]d'co8rKO3FJh#Vle8Z2L:-C)Z1T72[8C9MRF%3/CYPrA:Z=Yse*4*U"II1^]lY2=</[<UU7eXXUfF5mSft+=kWG!bj<]Mfnt[I9!M4rpBHsl66r;ImRB3B2au[7:aBI>("XM3U,`4qY91%[_C;g$,UbnPLqP+l?Y#dr.KgYc3R6Z#@(gA_:F$5.iU_mC'ljN9l`Y;SeqLgQo-6<1UPt-"o_CI6jI^lj"2/nUXK<!StPAnqq*V@@blj/_/3S+^<@=S0D^6Z;Z<K9DEL%c0;=dJD`K<SEDTtoZW/J^SbR<\&O]_i7i<E(i#Bdlnf`kr%=mo_'&8N)sCd%%,?32V.6:BLCPP.1XbKWPGdUUW_kM!Sn>MD.59n=j"RNF/$uM=Ho82?epcLPIfa@d2sj(`T5mNJ:*\"PP=XYs;m\,0YiN8HKY&9iT9RdRXj[T^=u6H*6VmjIkremA88_V50`\j(q*i`61P2=8]FjHU$f.UjerdhAI[tK+9%se(1>pZ/\\>3&j+.NcrpE[LK)#,%kcSd!r"cOH.?qc#Xa!j3I%c+hZPO*H-*4R[8SYetd0.gjl??<[\X@qUo%R5[*%!S.-/EfgtWnm3'D,.)ab-bjOutQtRk5VdDeTB:[6oFKKrW(gJt6[16jp.#Hk]*b'nOk^8Zs0g*mTgdk.@%:%q6hMV23o!WP:TA+]f_/?k'28jpb\te`/mQYotdCWoa]L)OpTDm13J$:7"d+ENudZ"pZ5(OOF\FimD"(mA@eqZluXYo04)a7'u.r%A8BPe*`+Z3h?)jH*=L/P5%<\QB&Z0[NRM#;%`I+21`+.4lbm$[QtpcMp,+t@:Mr$/R-CCEAK*PTcAC'>/%l-#u/N2NMuSZ60oj"L`TR[Q_Y3X0QcE]AA_9]K:QW#925qH$c9Ni,*RJHsgURuG@l+oPJ7Y)cl_QHOLtR@G)W\1t]`dGABA](oM2ds4WThEroo9"S8H\NP%P2U(SCm1%ggl+TlV4uh42lU,<P9dVi2#sE'4kRP,OAL9u*h#)E*_Qbr!O!7a1<>[uT/<\S_4O"?XGruKTSr4Eg@VaUi<9uue5M5>eHA<T[F+s,Bh\b86a6^&$lVQiTi;_ise(n#0l\"\Ah@H-%Q,HPgAJ3JY-`-Ht!T:=kj9d$&KFcmp)<)e^aq,r&YIMiFVS\A2K=f(9!beuI!C3_4aDXRMEAl4WEM\2f)5NA_l+<D1::$k9_oAHUC$\>:/kR^CB@D<fd=]tC$El^(6GH1[,2XtMKe/Kd/W5S!1b122]Q"L$GR`V]CK<ZZn0a-%XEtiE)-0bJNQin1Qnr%#RQ&91BEQ\MarEr3'mA4QAL(f/g^oLhb;SAMUfVMFO]W?<>)6HggMs<beq2oh+aC8CMq;q5AM$[7SIEN93Bu6Dq.K`"chTCZm.HRPe[KUK->VoKmkcndg[7D;LFb/lCCi&Es6"lZ_"&K>?l^%HXK>LiUYLQa4$/??#dk%G45k^\k7nGaDIN,`cB>i2.m@iiMANo=KEpQQMkYN;iLu0Z!(ZtaZi~>endstream
endobj
xref
0 13
0000000000 65535 f 
0000000061 00000 n 
0000000102 00000 n 
0000000209 00000 n 
0000000321 00000 n 
0000000515 00000 n 
0000000709 00000 n 
0000000903 00000 n 
0000000971 00000 n 
0000001251 00000 n 
0000001322 00000 n 
0000003883 00000 n 
0000006479 00000 n 
trailer
<<
/ID 
[<b8cb41546beec6ef1a94fcc6ee7defac><b8cb41546beec6ef1a94fcc6ee7defac>]
% ReportLab generated PDF document -- digest (opensource)

/Info 8 0 R
/Root 7 0 R
/Size 13
>>
startxref
8435
%%EOF

[Uploading 1) SOC 321.pdf…]()
