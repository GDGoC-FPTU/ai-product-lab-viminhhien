# 02 - Deep Dive Report

## Thong tin nhom

- Ten nhom: Dungpt
- Thanh vien:
  - Nguyen Minh Hien - MSSV: cap nhat theo thong tin ca nhan

---

## Quyet dinh lua chon

Nhom chon bai toan **Xanh SM xu ly su co xe dien sap het pin giua duong** de thuc hien Deep-Dive.

Ly do chon:

- Workflow hien tai ro rang, co the mo ta bang cac buoc van hanh cu the.
- Dau vao co cau truc: bien so xe, toa do GPS, muc pin, loai xe, danh sach tram sac va tinh trang tru sac.
- Tac dong kinh doanh do duoc bang thoi gian xu ly, thoi gian tai xe cho, ti le dieu huong dung va so chuyen bi cham/huy.
- Rui ro an toan co the kiem soat bang rule guard, human-in-the-loop va fallback thu cong.

---

## 3.1 Current-State Workflow

Quy trinh hien tai khi tai xe Xanh SM bao xe sap het pin:

```text
Tai xe goi tong dai
  -> Dieu phoi vien ghi nhan bien so, loai xe, muc pin
  -> Dieu phoi vien tra vi tri GPS tren ban do noi bo
  -> Dieu phoi vien mo dashboard tram sac VinFast
  -> Dieu phoi vien tim tram gan nhat, con tru trong, dung loai cong sac
  -> Dieu phoi vien soan tin nhan huong dan cho tai xe
  -> Neu pin qua thap, dieu phoi vien goi doi cuu ho pin di dong
```

Bang workflow hien tai:

| Buoc | Nguoi/He thong | Dau vao | Dau ra | Thoi gian TB | Ghi chu |
|---|---|---|---|---:|---|
| 1. Nhan cuoc goi su co | Dieu phoi vien | Cuoc goi tu tai xe | Log su co ban dau | 2 phut | Handoff tu tai xe sang tong dai |
| 2. Tra vi tri xe | Dieu phoi vien + ban do noi bo | Bien so/ID xe | Toa do GPS | 2 phut | Co the cham neu he thong ban do lag |
| 3. Tim tram sac phu hop | Dieu phoi vien + dashboard tram sac | GPS, loai xe, muc pin | Tram sac kha thi | 5 phut | Bottleneck: can so sanh nhieu dieu kien |
| 4. Soan huong dan | Dieu phoi vien | Tram sac va vi tri xe | Tin nhan huong dan | 5 phut | Bottleneck: de sai/khong ro |
| 5. Goi cuu ho neu can | Dieu phoi vien | Muc pin rat thap | Yeu cau cuu ho | 1 phut | Fallback thu cong |

Tong thoi gian xu ly trung binh: **15 phut/luot**.

Hai bottleneck chinh la **Buoc 3** va **Buoc 4**, tong cong khoang 10 phut/luot.

---

## 3.2 Problem Statement 6-Field

| Field | Noi dung chi tiet |
|---|---|
| 1. Actor / Operator | Dieu phoi vien trung tam van hanh Xanh SM, nguoi xu ly cuoc goi khan tu tai xe khi xe sap het pin hoac khong tim duoc tram sac phu hop. |
| 2. Current Workflow | Dieu phoi vien nhan cuoc goi, tra vi tri GPS xe, mo dashboard tram sac VinFast, tim tram con tru trong va dung loai cong sac, sau do viet tin nhan huong dan cho tai xe. Neu pin qua thap thi goi doi cuu ho pin di dong. |
| 3. Bottleneck | Buoc tim tram sac phu hop va soan huong dan mat 10-12 phut/luot. Dieu phoi vien phai xu ly nhieu thong tin cung luc: muc pin, khoang cach, loai cong sac, tinh trang tru sac, huong di va do khan cap. |
| 4. Business Impact | Moi ngay uoc tinh co 60-80 su co lien quan den pin tai khu vuc do thi lon. Voi 15 phut/luot, team dieu phoi mat khoang 15-20 gio cong/ngay. Thoi gian cho lau lam tai xe bi tre chuyen, tang nguy co huy chuyen va giam trai nghiem khach hang. |
| 5. Success Metric | Giam thoi gian xu ly tu 15 phut xuong duoi 3 phut/luot; 98% de xuat dung tram va dung loai cong sac; 100% tin nhan cho tai xe phai co dieu phoi vien duyet; 0 truong hop AI khuyen tai xe pin duoi 5% di den tram xa hon 5km. |
| 6. Operational Boundary | AI duoc doc du lieu dau vao, de xuat phuong an va tao draft tin nhan. AI khong duoc tu dong gui tin, khong duoc noi rang da gui tin, khong duoc tu suy doan du lieu thieu. Neu pin < 5% va tram xa > 5km, AI phai de xuat `dispatch_mobile_charger`. Moi draft phai bat dau bang `[DRAFT_ONLY]`. |

---

## 3.3 Future-State Flow & AI Fit

### AI Fit

Giai phap phu hop nhat la **LLM Feature + Rule Guard**, khong phai Agent tu tri.

Ly do:

- Quy trinh co cau truc ro va khong can Agent tu quyet dinh nhieu buoc.
- Rule guard xu ly dieu kien an toan nghiem ngat: pin < 5%, khoang cach > 5km, thieu du lieu.
- LLM co gia tri o buoc tong hop thong tin va viet huong dan tieng Viet ro rang cho tai xe.
- Con nguoi van phai duyet truoc khi gui, vi day la tinh huong van hanh co rui ro an toan.

### Future-State Flow

```text
Tai xe bao su co
  -> Dispatcher nhap/xac nhan bien so, muc pin, vi tri
  -> Rule Guard kiem tra dieu kien an toan
      -> Neu pin < 5% va tram xa > 5km: de xuat dispatch_mobile_charger
      -> Neu thieu du lieu: yeu cau bo sung thong tin
  -> AI lay danh sach tram sac phu hop va tao [DRAFT_ONLY]
  -> Dispatcher review/chinh sua
  -> Dispatcher bam gui cho tai xe
  -> Neu AI loi hoac khong tu tin: quay ve quy trinh thu cong
```

### Human-in-the-loop

Buoc HITL bat buoc nam o giai doan **review va phe duyet tin nhan**. AI chi tao ban nhap. Dieu phoi vien la nguoi chiu trach nhiem cuoi cung ve viec gui huong dan.

### Fallback

Neu AI khong co du du lieu, API tram sac loi, hoac output khong dung format:

- He thong hien canh bao cho dieu phoi vien.
- Khong gui bat ky tin nhan nao cho tai xe.
- Dieu phoi vien quay lai quy trinh thu cong: tra ban do, tra dashboard tram sac va goi cuu ho neu can.

---

## Phase 5 - Evaluate

### AI Readiness Checklist

| Cau hoi | Trang thai | Ghi chu |
|---|---|---|
| Co du lieu mau/log sach de test? | Co dieu kien | Can log su co pin, vi tri xe, danh sach tram sac, thoi gian xu ly va ket qua dieu phoi. |
| Rui ro khi AI sai co nam trong tam kiem soat? | Co | Co rule guard, HITL va fallback thu cong. AI khong duoc tu dong gui tin. |
| Stakeholders san sang thay doi quy trinh? | Co dieu kien | Dispatcher can duoc training de xem AI la co-pilot, khong phai nguoi thay the. |

### Quyet dinh cuoi cung

**GO - Bat dau xay dung prototype voi scope hep.**

Justification:

- Bai toan co tan suat lap lai cao va ton thoi gian ro rang.
- Loi AI co the kiem soat bang guardrail va human review.
- Scope prototype nho: chi tao draft va de xuat hanh dong an toan, khong tu dong dieu phoi.
- Chi phi ban dau thap: can mot API ket noi du lieu xe/tram sac, mot LLM call cho draft, va UI duyet cua dispatcher.

Uoc tinh chi phi prototype:

- 1 ky su backend + 1 ky su AI trong 2-3 tuan de ket noi API, rule guard va prompt prototype.
- 1 nhom dispatcher pilot trong 1 tuan de test 50-100 tinh huong.
- Chi phi LLM thap vi moi su co chi can 1-2 request ngan.

Dieu kien truoc khi rollout:

- Test it nhat 100 case thuc te hoac gia lap.
- Do chinh xac de xuat tram sac >= 98%.
- Khong co case nao vi pham rule pin < 5% va tram xa > 5km.
- Dispatcher xac nhan draft giup giam thoi gian xu ly xuong duoi 3 phut/luot.
