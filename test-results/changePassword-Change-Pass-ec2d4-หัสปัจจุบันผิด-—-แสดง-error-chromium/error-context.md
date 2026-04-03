# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: changePassword.spec.ts >> Change Password Feature >> ทดสอบการตรวจสอบการเปลี่ยนรหัสผ่านของผู้ใช้ >> Negative Cases >> TC-GUEST-12 | ทดสอบรหัสปัจจุบันผิด — แสดง error
- Location: tests\changePassword.spec.ts:41:11

# Error details

```
Error: expect(received).toContain(expected) // indexOf

Expected substring: "รหัสผ่านไม่ถูกต้อง"
Received string:    "บันทึกข้อมูลสำเร็จ!"
```

# Page snapshot

```yaml
- generic [ref=e2]:
  - generic [ref=e3]:
    - navigation [ref=e4]:
      - generic [ref=e5]:
        - generic [ref=e8]:
          - img [ref=e9]
          - textbox "ค้นหาการประกวด..." [ref=e11]
        - list [ref=e13]:
          - listitem [ref=e14]:
            - link "หน้าหลัก" [ref=e15] [cursor=pointer]:
              - /url: /
          - listitem [ref=e16]:
            - link "การประกวด" [ref=e17] [cursor=pointer]:
              - /url: /all-competitions
              - generic [ref=e18]: การประกวด
          - listitem [ref=e19]:
            - link "ประกาศผล" [ref=e20] [cursor=pointer]:
              - /url: /results
          - listitem [ref=e21]:
            - link "เกี่ยวกับเรา" [ref=e22] [cursor=pointer]:
              - /url: /about
        - link "เข้าสู่ระบบ" [ref=e24] [cursor=pointer]:
          - /url: /login
    - generic [ref=e29]:
      - generic [ref=e30]:
        - generic [ref=e31]:
          - heading "สมชาย รักอักษร" [level=1] [ref=e32]
          - paragraph [ref=e33]: somchai.ruk@email.com
        - button "แก้ไขข้อมูล" [ref=e34] [cursor=pointer]:
          - img [ref=e35]
          - text: แก้ไขข้อมูล
      - generic [ref=e38]:
        - heading "การประกวดที่ได้รับรางวัล" [level=4] [ref=e39]
        - generic [ref=e40]:
          - generic [ref=e42]:
            - generic [ref=e43]:
              - generic [ref=e45]: 🏆
              - generic [ref=e46]:
                - heading "การประกวดแต่งกลอนแปด หัวข้อ \"รำลึกกวีเอกสุนทรภู่\"" [level=6] [ref=e47]
                - paragraph [ref=e48]: ประกาศเมื่อ 26 มิถุนายน 2025
                - text: ชนะเลิศ
            - button "ดาวน์โหลดเกียรติบัตร" [ref=e49] [cursor=pointer]:
              - img [ref=e50]
              - text: ดาวน์โหลดเกียรติบัตร
          - generic [ref=e54]:
            - generic [ref=e55]:
              - generic [ref=e57]: 🏆
              - generic [ref=e58]:
                - heading "ประกวดโคลงสี่สุภาพ หัวข้อ \"วิถีไทยในยุคดิจิทัล\"" [level=6] [ref=e59]
                - paragraph [ref=e60]: ประกาศเมื่อ 10 กุมภาพันธ์ 2026
                - text: ชมเชย
            - button "ดาวน์โหลดเกียรติบัตร" [ref=e61] [cursor=pointer]:
              - img [ref=e62]
              - text: ดาวน์โหลดเกียรติบัตร
  - contentinfo [ref=e65]: Web application นี้ จัดทำเพื่อการศึกษาเท่านั้น
```

# Test source

```ts
  1  | import { expect, Page } from "@playwright/test";
  2  | 
  3  | export class ProfilePage {
  4  |   constructor(private page: Page) {}
  5  | 
  6  |   // ── Locators ─────────────────────────────────────────────────────────────
  7  |   private editButton() {
  8  |     return this.page.getByRole("button", { name: "แก้ไขข้อมูล" });
  9  |   }
  10 |   private saveButton() {
  11 |     return this.page.getByRole("button", { name: "บันทึกการเปลี่ยนแปลง" });
  12 |   }
  13 |   private currentPasswordInput() {
  14 |     return this.page.getByRole("textbox", {
  15 |       name: "กรอกรหัสผ่านปัจจุบันของคุณ",
  16 |     });
  17 |   }
  18 |   private newPasswordInput() {
  19 |     return this.page.getByRole("textbox", {
  20 |       name: "รหัสผ่านใหม่",
  21 |       exact: true,
  22 |     });
  23 |   }
  24 |   private confirmPasswordInput() {
  25 |     return this.page.getByRole("textbox", {
  26 |       name: "ยืนยันรหัสผ่านใหม่อีกครั้ง",
  27 |     });
  28 |   }
  29 | 
  30 |   // ── Actions ───────────────────────────────────────────────────────────────
  31 |   async goto() {
  32 |     await this.page.goto("/profile");
  33 |     await this.page.waitForLoadState("networkidle");
  34 |   }
  35 | 
  36 |   async openEditMode() {
  37 |     await this.editButton().click();
  38 |   }
  39 | 
  40 |   async changePassword(
  41 |     current: string,
  42 |     newPass: string,
  43 |     confirm: string,
  44 |   ): Promise<string> {
  45 |     await this.openEditMode();
  46 |     await this.currentPasswordInput().fill(current || "");
  47 |     await this.newPasswordInput().fill(newPass || "");
  48 |     await this.confirmPasswordInput().fill(confirm || "");
  49 | 
  50 |     let dialogMessage = "";
  51 | 
  52 |     // 1. เตรียมดักฟัง Dialog (Alert) ไว้ล่วงหน้า
  53 |     this.page.once("dialog", async (dialog) => {
  54 |       dialogMessage = dialog.message();
  55 |       await dialog.accept();
  56 |     });
  57 | 
  58 |     // 2. คลิกปุ่มบันทึก
  59 |     await this.saveButton().click({ force: true });
  60 | 
  61 |     // 3. รอสักนิดให้ระบบประมวลผล (ป้องกันหน้าเว็บหน่วง)
  62 |     await this.page.waitForTimeout(1000);
  63 | 
  64 |     // 4. ตรวจสอบ: ถ้ามี Dialog เด้งมา ให้คืนค่าจาก Dialog
  65 |     if (dialogMessage) {
  66 |       return dialogMessage;
  67 |     }
  68 | 
  69 |     // 5. ตรวจสอบ: ถ้าไม่มี Dialog ให้เช็ค HTML5 Validation ต่อ
  70 |     const validationError = await this.page.evaluate(() => {
  71 |       const inputs = Array.from(document.querySelectorAll("input"));
  72 |       const invalidInput = inputs.find(
  73 |         (input) => input.validationMessage !== "",
  74 |       );
  75 |       return invalidInput ? invalidInput.validationMessage : null;
  76 |     });
  77 | 
  78 |     return validationError || "No Dialog or Validation found";
  79 |   }
  80 | 
  81 |   // ── Assertions ────────────────────────────────────────────────────────────
  82 |   async expectSuccess(message: string) {
  83 |     expect(message).toContain("บันทึกข้อมูลสำเร็จ");
  84 |   }
  85 | 
  86 |   async expectError(message: string, expectedMessage: string) {
> 87 |     expect(message).toContain(expectedMessage);
     |                     ^ Error: expect(received).toContain(expected) // indexOf
  88 |   }
  89 | 
  90 |   async expectInEditMode() {
  91 |     await expect(this.saveButton()).toBeVisible();
  92 |   }
  93 | }
  94 | 
```