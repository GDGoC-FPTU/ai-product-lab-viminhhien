# 01 - Problem Scan & Quick Problem Cards

## Thong tin ca nhan

- Ho va ten: Pham The Dung
- Lop/Nhom: Vin Smart Future Lab 02
- Vai tro: AI Product Engineer tap su tai Vin Smart Future

---

## Phase 1 - SCAN: Danh sach bai toan co the ung dung AI

| #   | Subsidiary | Lens                | Mo ta ngan bai toan                                                                                                                                  |
| --- | ---------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Xanh SM    | Ton thoi gian       | Dieu phoi vien mat nhieu thoi gian xu ly su co xe dien sap het pin giua duong: tra vi tri xe, tim tram sac con tru trong, soan huong dan cho tai xe. |
| 2   | Vinhomes   | Lap lai             | Phan loai phan anh cu dan tren app Vinhomes Resident nhu mat nuoc, hong den, tieng on, ve sinh, roi chuyen ve dung bo phan xu ly.                    |
| 3   | VinFast    | AI-upgrade          | Khach hang mo ta loi xe bang tieng Viet doi thuong, nhan vien ky thuat phai doc va phan loai thu cong thanh nhom loi ban dau.                        |
| 4   | Vinpearl   | Pain tu stakeholder | Quan ly khach san phai doc review tu nhieu kenh nhu Google, Agoda, Booking de tim phan nan khan cap va giao cho bo phan lien quan.                   |
| 5   | Vinmec     | Ton thoi gian       | Bac si mat 20-30 phut moi benh nhan de viet tom tat xuat vien tu benh an, xet nghiem va ghi chu dieu tri.                                            |
| 6   | Xanh SM    | Lap lai             | Tong hop ly do huy chuyen tu ghi chu tai xe va cuoc goi CSKH de tim pattern gay ro ri doanh thu.                                                     |

---

## Phase 2 - QUICK-ASSESS: 3 Quick Problem Cards

### Quick Problem Card #1

**Bai toan:** Xanh SM xu ly su co xe dien sap het pin giua duong.

**Cong ty thanh vien:** Xanh SM

**Actor dang dau:** Dieu phoi vien trung tam van hanh va tai xe dang cho huong dan.

**Workflow thu cong hien tai:**

1. Tai xe goi tong dai bao pin yeu hoac khong tim duoc tram sac.
2. Dieu phoi vien tra bien so va vi tri GPS xe tren ban do noi bo.
3. Dieu phoi vien mo dashboard tram sac VinFast de tim tru con trong, dung loai cong sac va gan nhat.
4. Dieu phoi vien soan tin nhan huong dan duong di gui cho tai xe.
5. Neu pin qua thap, dieu phoi vien goi doi cuu ho pin di dong.

**Buoc ton thoi gian/loi nhieu nhat:** Buoc 3-4, khoang 10-12 phut/luot. Loi pho bien la chon tram xa, sai loai cong sac, hoac soan huong dan khong ro.

**AI co the ho tro o dau:** AI lay thong tin co cau truc tu vi tri xe, loai xe, muc pin, danh sach tram sac va tao draft huong dan ngan gon cho dieu phoi vien duyet.

**Metric thanh cong:** Giam tong thoi gian xu ly tu 15 phut xuong duoi 3 phut/luot; 98% de xuat dung tram va dung loai cong sac; 100% tin nhan co human review truoc khi gui.

**Quick Architecture:** LLM Feature + rule guard. Khong can Agent vi quy trinh co cau truc ro, nhung can LLM de soan huong dan tieng Viet de hieu.

---

### Quick Problem Card #2

**Bai toan:** Vinhomes phan loai va dieu huong phan anh cu dan.

**Cong ty thanh vien:** Vinhomes

**Actor dang dau:** Nhan vien CSKH va ban quan ly toa nha.

**Workflow thu cong hien tai:**

1. Cu dan gui phan anh tren app Vinhomes Resident.
2. CSKH doc noi dung, anh dinh kem va thong tin toa nha.
3. CSKH phan loai van de: ky thuat, ve sinh, an ninh, phi dich vu, tieng on.
4. CSKH chuyen ticket den dung bo phan va viet phan hoi ban dau.
5. Bo phan lien quan cap nhat trang thai xu ly.

**Buoc ton thoi gian/loi nhieu nhat:** Buoc 2-4, khoang 8-12 phut/ticket. Loi pho bien la route sai bo phan hoac phan hoi ban dau qua chung chung.

**AI co the ho tro o dau:** LLM phan loai noi dung, trich xuat dia diem/toa/tang, tao draft phan hoi ban dau va de xuat bo phan tiep nhan.

**Metric thanh cong:** 85% ticket duoc phan loai trong duoi 30 giay; giam thoi gian phan hoi dau tu 12 gio xuong duoi 2 gio; ti le route sai duoi 5%.

**Quick Architecture:** LLM Feature ket hop Rule router. Rule xu ly category ro rang, LLM xu ly noi dung mo ho va draft phan hoi.

---

### Quick Problem Card #3

**Bai toan:** Vinmec tao ban nhap tom tat xuat vien cho bac si.

**Cong ty thanh vien:** Vinmec

**Actor dang dau:** Bac si dieu tri va dieu duong hanh chinh.

**Workflow thu cong hien tai:**

1. Bac si mo benh an dien tu, ket qua xet nghiem, chi dinh thuoc va ghi chu dieu tri.
2. Bac si doc lai qua trinh nam vien va loc cac thong tin quan trong.
3. Bac si viet tom tat xuat vien bang ngon ngu de hieu cho benh nhan.
4. Bac si kiem tra, chinh sua va ky xac nhan.

**Buoc ton thoi gian/loi nhieu nhat:** Buoc 2-3, khoang 20-30 phut/benh nhan. Loi pho bien la thieu moc dieu tri, noi dung qua chuyen mon hoac sai thu tu thong tin.

**AI co the ho tro o dau:** AI tao ban nhap tom tat tu du lieu co san, nhan dien thong tin thieu va bat buoc bac si duyet truoc khi dua cho benh nhan.

**Metric thanh cong:** Giam thoi gian tao ban nhap tu 25 phut xuong duoi 7 phut; 100% ban tom tat phai duoc bac si duyet; khong tu dong dua ra chan doan moi.

**Quick Architecture:** LLM Feature voi HITL nghiem ngat. Khong dung Agent tu tri vi lien quan y te va rui ro an toan benh nhan.

---

## Lua chon bai toan de tiep tuc prototype

Toi chon **Quick Problem Card #1 - Xanh SM xu ly su co xe dien sap het pin giua duong** de lam prototype, vi:

- Bai toan co workflow ro, du lieu dau vao co cau truc: vi tri xe, loai xe, muc pin, danh sach tram sac.
- Tac dong van hanh do duoc bang thoi gian xu ly va ti le dieu huong dung.
- Rui ro co the kiem soat bang rule guard va human-in-the-loop: AI chi tao draft, khong tu dong gui tin cho tai xe.
