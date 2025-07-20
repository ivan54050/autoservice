# <!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Автосервіс: Клієнти, Авто, Роботи, Запчастини</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter&display=swap');
    body {
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
        Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
      background: #f0f4f8;
      margin: 0; padding: 20px;
      max-width: 900px;
      margin-left: auto;
      margin-right: auto;
      color: #222;
    }
    h1, h2 {
      color: #1565c0;
      font-weight: 700;
      margin-bottom: 12px;
    }
    section {
      background: #fff;
      padding: 20px 24px;
      border-radius: 12px;
      box-shadow: 0 8px 20px rgba(21, 101, 192, 0.1);
      margin-bottom: 30px;
    }
    input, select, button, textarea {
      font-family: inherit;
      font-size: 15px;
      padding: 10px 14px;
      margin-right: 10px;
      border: 2px solid #1565c0;
      border-radius: 8px;
      outline-offset: 2px;
      transition: border-color 0.25s ease;
    }
    input:focus, select:focus, textarea:focus {
      border-color: #0d47a1;
      box-shadow: 0 0 6px #0d47a1aa;
    }
    button {
      background: #1565c0;
      color: #fff;
      font-weight: 600;
      cursor: pointer;
      border: none;
      user-select: none;
      transition: background-color 0.3s ease;
    }
    button:hover:not(.delete) {
      background: #0d47a1;
    }
    button.delete {
      background: #d32f2f;
      padding: 6px 12px;
      border-radius: 50%;
      width: 32px;
      height: 32px;
      font-size: 20px;
      line-height: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: background-color 0.3s ease;
    }
    button.delete:hover {
      background: #9a2222;
    }
    ul {
      list-style: none;
      padding-left: 0;
      margin-top: 15px;
    }
    li {
      background: #e7f0fc;
      border-radius: 8px;
      padding: 12px 16px;
      margin-bottom: 10px;
      box-shadow: inset 0 0 8px #c3dafc;
    }
    .nested-list {
      margin-left: 20px;
      margin-top: 8px;
    }
    label {
      display: block;
      margin-top: 14px;
      margin-bottom: 6px;
      font-weight: 600;
    }
    .flex-row {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      margin-bottom: 10px;
    }
    .flex-row > * {
      margin-bottom: 10px;
    }
    small {
      color: #555;
    }
    @media (max-width: 480px) {
      input, select, button, textarea {
        flex: 1 1 100%;
        margin-right: 0;
      }
      .flex-row {
        flex-direction: column;
        align-items: stretch;
      }
      button {
        margin-top: 8px;
      }
    }
  </style>
</head>
<body>

<h1>Автосервіс: Клієнти, Авто, Роботи, Запчастини</h1>

<section id="clients-section">
  <h2>Клієнти</h2>
  <div class="flex-row">
    <input type="text" id="client-name" placeholder="Ім'я клієнта" />
    <button id="add-client">Додати клієнта</button>
  </div>
  <ul id="clients-list"></ul>
</section>

<section id="spares-section">
  <h2>Склад запчастин</h2>
  <div class="flex-row">
    <input type="text" id="spare-name" placeholder="Назва запчастини" />
    <input type="number" id="spare-qty" placeholder="Кількість" min="1" />
    <button id="add-spare">Додати запчастину</button>
  </div>
  <ul id="spares-list"></ul>
</section>

<script>
  let data = JSON.parse(localStorage.getItem('autoServiceData')) || {
    clients: [],
    spares: []
  };

  function saveData() {
    localStorage.setItem('autoServiceData', JSON.stringify(data));
  }

  function render() {
    renderClients();
    renderSpares();
  }

  // --- Запчастини ---
  const sparesListEl = document.getElementById('spares-list');
  const spareNameInput = document.getElementById('spare-name');
  const spareQtyInput = document.getElementById('spare-qty');
  const addSpareBtn = document.getElementById('add-spare');

  function renderSpares() {
    sparesListEl.innerHTML = '';
    if(data.spares.length === 0) {
      sparesListEl.innerHTML = '<li style="color:#999;">Список запчастин порожній</li>';
      return;
    }
    data.spares.forEach((spare, i) => {
      const li = document.createElement('li');
      li.textContent = `${spare.name} — Кількість: ${spare.qty}`;

      const delBtn = document.createElement('button');
      delBtn.textContent = '×';
      delBtn.className = 'delete';
      delBtn.title = 'Видалити запчастину';
      delBtn.onclick = () => {
        if(confirm(`Видалити запчастину "${spare.name}"?`)) {
          data.spares.splice(i,1);
          saveData();
          render();
        }
      };
      li.appendChild(delBtn);
      sparesListEl.appendChild(li);
    });
  }

  addSpareBtn.onclick = () => {
    const name = spareNameInput.value.trim();
    const qty = parseInt(spareQtyInput.value);
    if(!name) return alert('Введіть назву запчастини');
    if(!qty || qty < 1) return alert('Введіть коректну кількість');
    data.spares.push({name, qty});
    saveData();
    render();
    spareNameInput.value = '';
    spareQtyInput.value = '';
  };

  // --- Клієнти ---
  const clientsListEl = document.getElementById('clients-list');
  const clientNameInput = document.getElementById('client-name');
  const addClientBtn = document.getElementById('add-client');

  function renderClients() {
    clientsListEl.innerHTML = '';
    if(data.clients.length === 0) {
      clientsListEl.innerHTML = '<li style="color:#999;">Список клієнтів порожній</li>';
      return;
    }

    data.clients.forEach((client, ci) => {
      const li = document.createElement('li');

      // Заголовок клієнта з кнопкою видалення
      const clientHeader = document.createElement('div');
      clientHeader.style.display = 'flex';
      clientHeader.style.justifyContent = 'space-between';
      clientHeader.style.alignItems = 'center';

      const clientNameSpan = document.createElement('strong');
      clientNameSpan.textContent = client.name;
      clientHeader.appendChild(clientNameSpan);

      const delClientBtn = document.createElement('button');
      delClientBtn.textContent = '×';
      delClientBtn.className = 'delete';
      delClientBtn.title = 'Видалити клієнта';
      delClientBtn.onclick = () => {
        if(confirm(`Видалити клієнта "${client.name}"? Всі авто та роботи буде видалено.`)) {
          data.clients.splice(ci, 1);
          saveData();
          render();
        }
      };
      clientHeader.appendChild(delClientBtn);

      li.appendChild(clientHeader);

      // Форма додавання авто до клієнта
      const addCarDiv = document.createElement('div');
      addCarDiv.className = 'flex-row';
      addCarDiv.style.marginTop = '10px';

      const carModelInput = document.createElement('input');
      carModelInput.placeholder = "Модель авто";
      carModelInput.style.flex = '1';
      addCarDiv.appendChild(carModelInput);

      const addCarBtn = document.createElement('button');
      addCarBtn.textContent = 'Додати авто';
      addCarBtn.onclick = () => {
        const model = carModelInput.value.trim();
        if(!model) return alert('Введіть модель авто');
        if(!client.cars) client.cars = [];
        client.cars.push({ model, works: [] });
        saveData();
        render();
      };
      addCarDiv.appendChild(addCarBtn);

      li.appendChild(addCarDiv);

      // Список авто клієнта
      if(client.cars && client.cars.length > 0) {
        const carsUl = document.createElement('ul');
        carsUl.className = 'nested-list';

        client.cars.forEach((car, cIndex) => {
          const carLi = document.createElement('li');

          const carHeader = document.createElement('div');
          carHeader.style.display = 'flex';
          carHeader.style.justifyContent = 'space-between';
          carHeader.style.alignItems = 'center';

          const carName = document.createElement('span');
          carName.textContent = car.model;
          carHeader.appendChild(carName);

          const delCarBtn = document.createElement('button');
          delCarBtn.textContent = '×';
          delCarBtn.className = 'delete';
          delCarBtn.title = 'Видалити авто';
          delCarBtn.onclick = () => {
            if(confirm(`Видалити авто "${car.model}"? Всі роботи буде видалено.`)) {
              client.cars.splice(cIndex, 1);
              saveData();
              render();
            }
          };
          carHeader.appendChild(delCarBtn);

          carLi.appendChild(carHeader);

          // Форма додавання робіт для авто
          const addWorkDiv = document.createElement('div');
          addWorkDiv.className = 'flex-row';
          addWorkDiv.style.marginTop = '8px';

          const workDescInput = document.createElement('input');
          workDescInput.placeholder = "Опис роботи";
          workDescInput.style.flex = '1';
          addWorkDiv.appendChild(workDescInput);

          const addWorkBtn = document.createElement('button');
          addWorkBtn.textContent = 'Додати роботу';
          addWorkBtn.onclick = () => {
            const desc = workDescInput.value.trim();
            if(!desc) return alert('Введіть опис роботи');
            if(!car.works) car.works = [];
            car.works.push(desc);
            saveData();
            render();
          };
          addWorkDiv.appendChild(addWorkBtn);

          carLi.appendChild(addWorkDiv);

          // Список робіт
          if(car.works && car.works.length > 0) {
            const worksUl = document.createElement('ul');
            worksUl.className = 'nested-list';

            car.works.forEach((workDesc, wIndex) => {
              const workLi = document.createElement('li');
              workLi.textContent = workDesc;

              const delWorkBtn = document.createElement('button');
              delWorkBtn.textContent = '×';
              delWorkBtn.className = 'delete';
              delWorkBtn.title = 'Видалити роботу';
              delWorkBtn.onclick = () => {
                if(confirm(`Видалити роботу "${workDesc}"?`)) {
                  car.works.splice(wIndex, 1);
                  saveData();
                  render();
                }
              };
              workLi.appendChild(delWorkBtn);

              worksUl.appendChild(workLi);
            });

            carLi.appendChild(worksUl);
          }

          carsUl.appendChild(carLi);
        });

        li.appendChild(carsUl);
      }

      clientsListEl.appendChild(li);
    });
  }

  addClientBtn.onclick = () => {
    const name = clientNameInput.value.trim();
    if(!name) return alert("Введіть ім'я клієнта");
    data.clients.push({ name, cars: [] });
    saveData();
    render();
    clientNameInput.value = '';
  };

  render();
</script>

</body>
</html>
