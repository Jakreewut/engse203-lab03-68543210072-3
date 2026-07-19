LAB 03 — Responsive Web UI & Form Interaction

  นาย จักรีวุฒน์ สุขคำเมือง 68543210072-3  

  คำอธิบาย - สร้างหน้าเว็บ responsive สำหรับระบบจัดการงานขนาดย่อม ฝึก semantic HTML, CSS layout, mobile-first design, event และ form validation.  



ลิ้ง github page

https://jakreewut.github.io/engse203-lab03-68543210072-3/  

  ภาพประกอบ   

  <img width="1919" height="1012" alt="image" src="https://github.com/user-attachments/assets/4b29fc67-75ee-4f83-82a4-8d6e4d2924cf" />
  

  <img width="1919" height="1032" alt="image" src="https://github.com/user-attachments/assets/f7ad4344-93e7-4140-a2b6-5a35e094d9d9" />


<img width="1919" height="1031" alt="image" src="https://github.com/user-attachments/assets/bf3f85d5-e69c-4a76-8593-9b9615841311" />



<img width="1168" height="1027" alt="image" src="https://github.com/user-attachments/assets/75a1b858-5f10-4103-ab36-0557713d499a" />



<img width="499" height="1035" alt="image" src="https://github.com/user-attachments/assets/ab781a68-b1dd-492b-8cf6-00610e552061" />



วิธีติ้งตั้ง 

-npm run build

-npm run dev


source code

<img width="284" height="312" alt="image" src="https://github.com/user-attachments/assets/c0917ae4-e298-424c-861e-8d98bbce6c76" />


      import './style.css';
    
    const form = document.querySelector('#request-form');
    
    // TODO 1: query preview/status/list elements
    const preview = {
      name: document.querySelector('#preview-name'),
      type: document.querySelector('#preview-type'),
      details: document.querySelector('#preview-details'),
    };
    const statusElement = document.querySelector('#form-status');
    const listElement = document.querySelector('#request-list');
    const requests = [];
    
    
    // TODO 2: readForm()
    function readForm() {
      return Object.fromEntries(new FormData(form).entries());
    }
    
    // TODO 3: renderPreview(data)
    function renderPreview(data) {
      preview.name.textContent = data.displayName || 'ยังไม่ระบุชื่อ';
      preview.type.textContent = data.requestType || 'ยังไม่เลือกประเภท';
      preview.details.textContent = data.requestDetails || 'ยังไม่มีรายละเอียด';
    }
    
    // TODO 4: validate(data)
    function validate(data) {
      const errors = {};
      if (data.displayName.trim().length < 2) {
        errors.displayName = 'กรุณากรอกชื่ออย่างน้อย 2 ตัวอักษร';
      }
      if (!data.requestType) {
        errors.requestType = 'กรุณาเลือกประเภทคำขอ';
      }
      if (data.requestDetails.trim().length < 10) {
        errors.requestDetails = 'กรุณากรอกรายละเอียดอย่างน้อย 10 ตัวอักษร';
      }
      return errors;
    }
    
    // TODO 5: renderErrors(errors)
    function renderErrors(errors) {
      for (const name of ['displayName', 'requestType', 'requestDetails']) {
        const field = form.elements[name];
        const output = document.querySelector(`#${name}-error`);
        const message = errors[name] || '';
    
        output.textContent = message;
        field.setAttribute('aria-invalid', String(Boolean(message)));
      }
    }
    
    function renderStatus(state, message) {
      statusElement.dataset.state = state;
      statusElement.textContent = message;
    }
    
    function renderList() {
      listElement.innerHTML = '';
      if (requests.length === 0) {
        const li = document.createElement('li');
        li.textContent = 'ยังไม่มีคำร้องขอ';
        listElement.appendChild(li);
        return;
      }
      requests.forEach(req => {
        const li = document.createElement('li');
        li.innerHTML = `<strong>${req.displayName}</strong> (${req.requestType}): <span>${req.requestDetails}</span>`;
        listElement.appendChild(li);
      });
    }
    
    
    // TODO 6: input and submit listeners
    form.addEventListener('input', () => {
      const data = readForm();
      renderPreview(data);
    });
    
    form.addEventListener('submit', (event) => {
      event.preventDefault();
      const data = readForm();
      const errors = validate(data);
      renderErrors(errors);
    
      if (Object.keys(errors).length > 0) {
        renderStatus('invalid', 'ยังบันทึกไม่ได้ กรุณาตรวจสอบข้อมูล');
        form.querySelector('[aria-invalid="true"]')?.focus();
        return;
      }
    
      requests.push(data);
      renderList();
    
      renderStatus('success', `ส่งคำร้องขอของ ${data.displayName} เรียบร้อย`);
      form.reset();
    });
    
    form.addEventListener('reset', () => {
      // ใช้ queueMicrotask เพื่อให้แน่ใจว่าฟอร์ม reset ตัวเองเสร็จก่อน
      // แล้วจึงอ่านค่าจากฟอร์ม (ที่ว่างเปล่า) เพื่ออัปเดต UI
      queueMicrotask(() => {
        renderPreview(readForm());
        renderErrors({});
        renderStatus('idle', 'กรุณากรอกข้อมูลคำร้องขอ');
      });
    });
    
    renderStatus('idle', 'กรุณากรอกข้อมูลคำร้องขอ');
    renderList();



index.html

    <!doctype html>
    <html lang="th">
    <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>ENGSE203 LAB 3</title>
      <script type="module" src="/src/main.js"></script>
    </head>
    <body>
      <header class="hero">
        <div class="container">
          <p class="eyebrow">ENGSE203 • LAB 3</p>
          <h1>Campus Service Request</h1>
          <p>Responsive Web UI, Event and Form</p>
        </div>
      </header>
    
      <main class="container page-grid">
        <section class="panel" aria-labelledby="form-title">
          <h2 id="form-title">Create Service Request</h2>
          <form id="request-form" novalidate>
            <!-- TODO 1: Requester Name field -->
             <div class="field">
              <label for="display-name">ชื่อที่ต้องการแสดง</label>
              <input
                id="display-name"
                name="displayName"
                type="text"
                minlength="2"
                placeholder="เช่น สมชาย ใจดี"
                aria-describedby="displayName-hint displayName-error"
                required />
              <small id="displayName-hint" class="hint">อย่างน้อย 2 ตัวอักษร</small>
              <small id="displayName-error" class="error"></small>
            </div>
            <!-- TODO 2: Request Type select -->
            <div class="field">
              <label for="request-type">ประเภทคำขอ</label>
              <select id="request-type" name="requestType" required>
                <option value="">-- กรุณาเลือก --</option>
                <option value="maintenance">ซ่อมแซม</option>
                <option value="cleaning">ทำความสะอาด</option>
                <option value="other">อื่นๆ</option>
              </select>
              <small id="requestType-error" class="error"></small>
            </div>
            <!-- TODO 3: Details textarea -->
            <div class="field">
              <label for="request-details">รายละเอียด</label>
              <textarea
                id="request-details"
                name="requestDetails"
                rows="4"
                placeholder="กรุณากรอกรายละเอียดของคำขอ..."
                aria-describedby="requestDetails-hint requestDetails-error"
                required></textarea>
              <small id="requestDetails-hint" class="hint">กรุณากรอกรายละเอียดอย่างน้อย 10 ตัวอักษร</small>
              <small id="requestDetails-error" class="error"></small>
            </div>
            <div class="actions">
              <button type="submit">Submit Request</button>
              <button type="reset" class="button-secondary">Clear</button>
            </div>
            <p id="form-status" class="status" role="status"></p>
          </form>
        </section>
    
        <aside class="panel preview-panel" aria-labelledby="preview-title">
          <h2 id="preview-title">Live Preview</h2>
          <dl id="preview" aria-live="polite">
            <div><dt>Name</dt><dd id="preview-name">ยังไม่ระบุชื่อ</dd></div>
            <div><dt>Type</dt><dd id="preview-type">ยังไม่เลือกประเภท</dd></div>
            <div><dt>Details</dt><dd id="preview-details">ยังไม่มีรายละเอียด</dd></div>
          </dl>
          <h3>Submitted Requests</h3>
          <ul id="request-list" class="request-list"></ul>
        </aside>
      </main>
    </body>
    </html><!doctype html>
    <html lang="th">
    <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>ENGSE203 LAB 3</title>
      <script type="module" src="/src/main.js"></script>
    </head>
    <body>
      <header class="hero">
        <div class="container">
          <p class="eyebrow">ENGSE203 • LAB 3</p>
          <h1>Campus Service Request</h1>
          <p>Responsive Web UI, Event and Form</p>
        </div>
      </header>
    
      <main class="container page-grid">
        <section class="panel" aria-labelledby="form-title">
          <h2 id="form-title">Create Service Request</h2>
          <form id="request-form" novalidate>
            <!-- TODO 1: Requester Name field -->
             <div class="field">
              <label for="display-name">ชื่อที่ต้องการแสดง</label>
              <input
                id="display-name"
                name="displayName"
                type="text"
                minlength="2"
                placeholder="เช่น สมชาย ใจดี"
                aria-describedby="displayName-hint displayName-error"
                required />
              <small id="displayName-hint" class="hint">อย่างน้อย 2 ตัวอักษร</small>
              <small id="displayName-error" class="error"></small>
            </div>
            <!-- TODO 2: Request Type select -->
            <div class="field">
              <label for="request-type">ประเภทคำขอ</label>
              <select id="request-type" name="requestType" required>
                <option value="">-- กรุณาเลือก --</option>
                <option value="maintenance">ซ่อมแซม</option>
                <option value="cleaning">ทำความสะอาด</option>
                <option value="other">อื่นๆ</option>
              </select>
              <small id="requestType-error" class="error"></small>
            </div>
            <!-- TODO 3: Details textarea -->
            <div class="field">
              <label for="request-details">รายละเอียด</label>
              <textarea
                id="request-details"
                name="requestDetails"
                rows="4"
                placeholder="กรุณากรอกรายละเอียดของคำขอ..."
                aria-describedby="requestDetails-hint requestDetails-error"
                required></textarea>
              <small id="requestDetails-hint" class="hint">กรุณากรอกรายละเอียดอย่างน้อย 10 ตัวอักษร</small>
              <small id="requestDetails-error" class="error"></small>
            </div>
            <div class="actions">
              <button type="submit">Submit Request</button>
              <button type="reset" class="button-secondary">Clear</button>
            </div>
            <p id="form-status" class="status" role="status"></p>
          </form>
        </section>
    
        <aside class="panel preview-panel" aria-labelledby="preview-title">
          <h2 id="preview-title">Live Preview</h2>
          <dl id="preview" aria-live="polite">
            <div><dt>Name</dt><dd id="preview-name">ยังไม่ระบุชื่อ</dd></div>
            <div><dt>Type</dt><dd id="preview-type">ยังไม่เลือกประเภท</dd></div>
            <div><dt>Details</dt><dd id="preview-details">ยังไม่มีรายละเอียด</dd></div>
          </dl>
          <h3>Submitted Requests</h3>
          <ul id="request-list" class="request-list"></ul>
        </aside>
      </main>
    </body>
    </html>

style.css

    *, *::before, *::after { box-sizing: border-box; }
    
    :root {
      --navy: #a7cf13;
      --blue: #ace42b;
      --cyan: #31d0ec;
      --yellow: #53e0e0;
      --text: #14213a;
      --muted: #5b6b85;
      --surface: #df82d7;
      --surface-alt: #fffaef;
      --background: #f4f7fc;
      --border: #cbd9ef;
      --danger: #b42318;
      --success: #157347;
      --shadow: 0 18px 45px rgb(26 58 112 / 0.12);
      --radius: 1.15rem;
    }
    
    html { color-scheme: light; }
    body {
      margin: 0;
      min-height: 100vh;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      color: var(--text);
      background:
        radial-gradient(circle at 15% 0%, rgba(56, 173, 194, 0.12), transparent 28rem),
        var(--background);
    }
    
    button, input, select, textarea { font: inherit; }
    .container { width: min(100% - 2rem, 72rem); margin-inline: auto; }
    .hero {
      color: white;
      background:
        linear-gradient(110deg, rgba(224, 44, 44, 0.96), rgb(29 99 213 / .90)),
        linear-gradient(45deg, var(--navy), var(--blue));
      padding: 2.25rem 0 2.5rem;
      border-bottom: 6px solid var(--cyan);
    }
    .hero__content { max-width: 54rem; }
    .eyebrow {
      margin: 0 0 .45rem;
      font-size: .8rem;
      font-weight: 850;
      letter-spacing: .12em;
      text-transform: uppercase;
    }
    .hero h1 { margin: 0; font-size: clamp(2.15rem, 7vw, 4rem); line-height: 1.05; }
    .hero__lead { margin: .85rem 0 0; max-width: 50rem; font-size: clamp(1rem, 2.4vw, 1.25rem); color: #e9f4ff; }
    
    .page-grid { display: grid; grid-template-columns: 1fr; gap: 1rem; padding-block: 1.25rem 2.5rem; }
    .panel { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 1.2rem; box-shadow: var(--shadow); }
    
    .field { display: grid; gap: .45rem; margin-bottom: 1rem; }
    .field label { font-weight: 750; }
    input, select, textarea {
      width: 100%;
      border: 1px solid #d29ea5;
      border-radius: .75rem;
      background: white;
      color: var(--text);
      padding: .8rem .9rem;
    }
    textarea { resize: vertical; min-height: 8rem; }
    input:hover, select:hover, textarea:hover { border-color: var(--blue); }
    input:focus-visible, select:focus-visible, textarea:focus-visible, button:focus-visible {
      outline: 3px solid var(--yellow);
      outline-offset: 2px;
    }
    input[aria-invalid="true"], select[aria-invalid="true"], textarea[aria-invalid="true"] { border-color: var(--danger); background: #fff8f7; }
    .hint { color: var(--muted); }
    .error { min-height: 1.2rem; color: var(--danger); font-weight: 650; }
    
    .actions { display: flex; flex-wrap: wrap; gap: .75rem; }
    button {
      border: 0;
      border-radius: .75rem;
      padding: .78rem 1.05rem;
      font-weight: 800;
      color: white;
      background: linear-gradient(135deg, var(--blue), #164aa4);
      cursor: pointer;
      b.status[data-state="success"] { color: var(--success); }
    .status[data-state="idle"] { color: var(--muted); }
    
    .preview-panel { background: linear-gradient(180deg, #072b42, #bed4f0); }
    
    #preview {
      display: grid;
      gap: .75rem;
      padding: 1.25rem;
      border-radius: 1rem;
      color: white;
      background:
        radial-gradient(circle at 85% 15%, rgb(22 184 212 / .75), transparent 8rem),
        linear-gradient(145deg, var(--navy), var(--blue));
      box-shadow: 0 16px 35px rgb(9 38 93 / .25);
      overflow-wrap: anywhere;
    }
    #preview dt {
      margin-bottom: .25rem;
      font-size: .75rem;
      font-weight: 850;
      letter-spacing: .12em;
      color: #cdefff;
      text-transform: uppercase;
    }
    #preview dd {
      margin: 0;
      font-weight: 500;
      font-size: 1.1rem;
    }
    
    .request-list {
      list-style: none;
      padding: 0;
      margin: 1rem 0 0;
      display: grid;
      gap: .75rem;
    }
    .request-list li {
      padding: .75rem 1rem;
      border-radius: .75rem;
      background: var(--surface-alt);
      border: 1px solid var(--border);
      overflow-wrap: anywhere;
    }
    
    footer { padding: 1.2rem 0 2rem; color: var(--muted); text-align: center; }
    
    @media (min-width: 48rem) {
      .page-grid { grid-template-columns: minmax(0, 1.5fr) minmax(20rem, 1fr); align-items: start; gap: 1.25rem; padding-top: 1.6rem; }
      .panel { padding: 1.5rem; }
      .preview-panel { position: sticky; top: 1rem; }
    }ox-shadow: 0 8px 18px rgb(29 99 213 / .2);
    }
    button:hover { transform: translateY(-1px); }
    .button-secondary { color: var(--navy); background: #e8eff9; box-shadow: none; }
    .status { min-height: 1.5rem; margin: .9rem 0 0; font-weight: 750; }
    .status[data-state="invalid"] { color: var(--danger); }

vite.config.js

    import { defineConfig } from 'vite';
    
    export default defineConfig({
      base: '/engse203-lab03-68543210072-3/',
      build: {
        outDir: 'docs',
        emptyOutDir: true,
      },
    });


