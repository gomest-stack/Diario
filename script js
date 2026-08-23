/**
 * DIÁRIO ESCOLAR - LÓGICA DE NEGÓCIO E INTERFACE
 */

// --- CONFIGURAÇÃO DE REGRAS DE NEGÓCIO (EDITÁVEL) ---
const CONFIG = {
    PASSING_GRADE: 6.0, // Média para aprovação
    // Fórmula customizada para cálculo da média
    calculateAverage: function (av1, av2, trabalho, recuperacao) {
        const n1 = parseFloat(av1) || 0;
        const n2 = parseFloat(av2) || 0;
        const nt = parseFloat(trabalho) || 0;
        
        let mediaRegular = (n1 + n2 + nt) / 3;

        // Aplicação de nota de recuperação caso exista
        if (recuperacao !== null && recuperacao !== undefined && recuperacao !== '') {
            const rec = parseFloat(recuperacao) || 0;
            // Se a nota de recuperação for maior, substitui a menor avaliação regular
            if (rec > mediaRegular) {
                return Math.max(mediaRegular, rec);
            }
        }
        return mediaRegular;
    }
};

// --- DADOS INICIAIS DE EXEMPLO (MOCK DATA) ---
const INITIAL_DATA = [
    {
        id: "turma-1",
        name: "7º Ano A",
        discipline: "Ciências",
        shift: "Manhã",
        year: 2026,
        students: [
            { id: "al-1", name: "Ana Silva", registration: "2026001", cpf: "111.222.333-04", birth: "2013-05-12", av1: 8.0, av2: 7.0, work: 9.0, rec: null, totalClasses: 20, absences: 2, dateAttendance: {} },
            { id: "al-2", name: "João Santos", registration: "2026002", cpf: "222.333.444-05", birth: "2013-08-20", av1: 5.0, av2: 6.0, work: 7.0, rec: null, totalClasses: 20, absences: 4, dateAttendance: {} },
            { id: "al-3", name: "Maria Souza", registration: "2026003", cpf: "", birth: "2013-01-15", av1: 9.5, av2: 9.0, work: 10.0, rec: null, totalClasses: 20, absences: 0, dateAttendance: {} },
            { id: "al-4", name: "Pedro Oliveira", registration: "2026004", cpf: "", birth: "2013-11-30", av1: 4.0, av2: 5.0, work: 5.0, rec: 6.5, totalClasses: 20, absences: 3, dateAttendance: {} },
            { id: "al-5", name: "Lucas Almeida", registration: "2026005", cpf: "", birth: "2013-03-08", av1: 3.0, av2: 4.0, work: 5.0, rec: 4.0, totalClasses: 20, absences: 6, dateAttendance: {} }
        ]
    }
];

// --- ESTADO DA APLICAÇÃO ---
let state = {
    currentUser: null,
    classes: [],
    currentClassId: null,
    searchQuery: ""
};

// --- GERENCIAMENTO DE ARMAZENAMENTO LOCAL (LOCALSTORAGE) ---
const Storage = {
    loadClasses: function () {
        const data = localStorage.getItem("diario_escolar_classes");
        if (!data) {
            localStorage.setItem("diario_escolar_classes", JSON.stringify(INITIAL_DATA));
            return INITIAL_DATA;
        }
        return JSON.parse(data);
    },
    saveClasses: function (classes) {
        localStorage.setItem("diario_escolar_classes", JSON.stringify(classes));
    },
    loadUser: function () {
        return JSON.parse(localStorage.getItem("diario_escolar_user"));
    },
    saveUser: function (user) {
        if (user) {
            localStorage.setItem("diario_escolar_user", JSON.stringify(user));
        } else {
            localStorage.removeItem("diario_escolar_user");
        }
    }
};

// --- INICIALIZAÇÃO DO SISTEMA ---
document.addEventListener("DOMContentLoaded", () => {
    state.classes = Storage.loadClasses();
    state.currentUser = Storage.loadUser();

    initEventListeners();

    if (state.currentUser) {
        showAppScreen();
    } else {
        showLoginScreen();
    }
});

// --- MAPEAMENTO DE EVENTOS DA INTERFACE ---
function initEventListeners() {
    // Login e Logout
    document.getElementById("login-form").addEventListener("submit", handleLogin);
    document.getElementById("btn-logout").addEventListener("click", handleLogout);

    // Navegação Turmas
    document.getElementById("btn-add-class").addEventListener("click", () => openClassModal());
    document.getElementById("btn-back-classes").addEventListener("click", showClassesList);

    // Ações na Turma
    document.getElementById("btn-edit-class").addEventListener("click", () => openClassModal(state.currentClassId));
    document.getElementById("btn-delete-class").addEventListener("click", handleDeleteClass);
    document.getElementById("btn-add-student").addEventListener("click", () => openStudentModal());

    // Formulários Modal
    document.getElementById("form-class").addEventListener("submit", handleSaveClass);
    document.getElementById("form-student").addEventListener("submit", handleSaveStudent);

    // Fechar Modais
    document.querySelectorAll(".btn-modal-close").forEach(btn => {
        btn.addEventListener("click", closeModals);
    });

    // Abas
    document.querySelectorAll(".tab-btn").forEach(btn => {
        btn.addEventListener("click", (e) => {
            document.querySelectorAll(".tab-btn").forEach(b => b.classList.remove("active"));
            document.querySelectorAll(".tab-content").forEach(c => c.classList.remove("active"));
            e.target.classList.add("active");
            document.getElementById(e.target.dataset.tab).classList.add("active");
        });
    });

    // Busca
    document.getElementById("search-student-input").addEventListener("input", (e) => {
        state.searchQuery = e.target.value.toLowerCase();
        renderCurrentClassTabs();
    });

    // Frequência por data
    document.getElementById("btn-load-date-attendance").addEventListener("click", loadDateAttendance);
    document.getElementById("btn-save-date-attendance").addEventListener("click", saveDateAttendance);
}

// --- AUTENTICAÇÃO ---
function handleLogin(e) {
    e.preventDefault();
    const userInput = document.getElementById("username").value.trim();
    const passInput = document.getElementById("password").value.trim();
    const errorDiv = document.getElementById("login-error");

    if (userInput === "2026" && passInput === "2026") {
        state.currentUser = { name: "Thyago", username: "2026" };
        Storage.saveUser(state.currentUser);
        errorDiv.innerText = "";
        showAppScreen();
    } else {
        errorDiv.innerText = "Login ou senha inválidos!";
    }
}

function handleLogout() {
    state.currentUser = null;
    Storage.saveUser(null);
    showLoginScreen();
}

function showLoginScreen() {
    document.getElementById("login-screen").classList.remove("hidden");
    document.getElementById("app-screen").classList.add("hidden");
}

function showAppScreen() {
    document.getElementById("login-screen").classList.add("hidden");
    document.getElementById("app-screen").classList.remove("hidden");
    document.getElementById("user-display-name").innerText = state.currentUser.name;
    showClassesList();
}

// --- GESTÃO DE TURMAS ---
function showClassesList() {
    state.currentClassId = null;
    document.getElementById("view-classes").classList.remove("hidden");
    document.getElementById("view-class-detail").classList.add("hidden");
    renderClassesGrid();
}

function renderClassesGrid() {
    const grid = document.getElementById("classes-grid");
    grid.innerHTML = "";

    if (state.classes.length === 0) {
        grid.innerHTML = `<p class="text-muted">Nenhuma turma cadastrada.</p>`;
        return;
    }

    state.classes.forEach(c => {
        const card = document.createElement("div");
        card.className = "class-card";
        card.innerHTML = `
            <div>
                <h3>${escapeHtml(c.discipline)} — ${escapeHtml(c.name)}</h3>
                <p>Turno: ${escapeHtml(c.shift)} | Ano: ${c.year}</p>
            </div>
            <div>
                <div class="card-meta">${c.students.length} alunos</div>
                <button class="btn btn-primary btn-sm btn-block" onclick="openClassDetail('${c.id}')">Acessar turma</button>
            </div>
        `;
        grid.appendChild(card);
    });
}

function openClassModal(classId = null) {
    const modal = document.getElementById("modal-class");
    const form = document.getElementById("form-class");
    
    if (classId) {
        const c = state.classes.find(item => item.id === classId);
        document.getElementById("modal-class-title").innerText = "Editar Turma";
        document.getElementById("class-id").value = c.id;
        document.getElementById("class-name").value = c.name;
        document.getElementById("class-discipline").value = c.discipline;
        document.getElementById("class-shift").value = c.shift;
        document.getElementById("class-year").value = c.year;
    } else {
        document.getElementById("modal-class-title").innerText = "Nova Turma";
        form.reset();
        document.getElementById("class-id").value = "";
    }
    
    modal.classList.remove("hidden");
}

function handleSaveClass(e) {
    e.preventDefault();
    const id = document.getElementById("class-id").value;
    const name = document.getElementById("class-name").value.trim();
    const discipline = document.getElementById("class-discipline").value.trim();
    const shift = document.getElementById("class-shift").value;
    const year = parseInt(document.getElementById("class-year").value);

    if (id) {
        const c = state.classes.find(item => item.id === id);
        c.name = name;
        c.discipline = discipline;
        c.shift = shift;
        c.year = year;
    } else {
        const newClass = {
            id: "turma-" + Date.now(),
            name,
            discipline,
            shift,
            year,
            students: []
        };
        state.classes.push(newClass);
    }

    Storage.saveClasses(state.classes);
    closeModals();

    if (state.currentClassId) {
        renderClassHeader();
    } else {
        renderClassesGrid();
    }
}

function handleDeleteClass() {
    if (!state.currentClassId) return;
    if (confirm("Tem certeza que deseja excluir esta turma e todos os seus alunos?")) {
        state.classes = state.classes.filter(c => c.id !== state.currentClassId);
        Storage.saveClasses(state.classes);
        showClassesList();
    }
}

// --- DETALHES DA TURMA E NAVEGAÇÃO INTERNA ---
function openClassDetail(classId) {
    state.currentClassId = classId;
    document.getElementById("view-classes").classList.add("hidden");
    document.getElementById("view-class-detail").classList.remove("hidden");
    renderClassHeader();
    renderCurrentClassTabs();
}

function renderClassHeader() {
    const c = getCurrentClass();
    if (!c) return;

    document.getElementById("detail-class-name").innerText = `${c.discipline.toUpperCase()} — ${c.name.toUpperCase()}`;
    document.getElementById("detail-class-info").innerText = `Professor: ${state.currentUser.name} | Turno: ${c.shift} | Ano Letivo: ${c.year}`;

    // Estatísticas do resumo
    let totalStudents = c.students.length;
    let sumAverages = 0;
    let approvedCount = 0;
    let recoveryCount = 0;
    let totalAbsences = 0;

    c.students.forEach(s => {
        const avg = CONFIG.calculateAverage(s.av1, s.av2, s.work, s.rec);
        sumAverages += avg;
        if (avg >= CONFIG.PASSING_GRADE) {
            approvedCount++;
        } else {
            recoveryCount++;
        }
        totalAbsences += (parseInt(s.absences) || 0);
    });

    const overallAverage = totalStudents > 0 ? (sumAverages / totalStudents).toFixed(1) : "0.0";

    document.getElementById("stat-total-students").innerText = totalStudents;
    document.getElementById("stat-class-avg").innerText = overallAverage.replace('.', ',');
    document.getElementById("stat-approved").innerText = approvedCount;
    document.getElementById("stat-recovery").innerText = recoveryCount;
    document.getElementById("stat-total-absences").innerText = totalAbsences;
}

function renderCurrentClassTabs() {
    renderStudentsTable();
    renderGradesTable();
    renderAttendanceTable();
}

function getCurrentClass() {
    return state.classes.find(c => c.id === state.currentClassId);
}

function getFilteredStudents(students) {
    if (!state.searchQuery) return students;
    return students.filter(s => 
        s.name.toLowerCase().includes(state.searchQuery) || 
        s.registration.toLowerCase().includes(state.searchQuery)
    );
}

// --- ABA ALUNOS ---
function renderStudentsTable() {
    const c = getCurrentClass();
    const tbody = document.getElementById("table-students-body");
    tbody.innerHTML = "";

    const students = getFilteredStudents(c.students);

    if (students.length === 0) {
        tbody.innerHTML = `<tr><td colspan="6" class="text-muted">Nenhum aluno encontrado.</td></tr>`;
        return;
    }

    students.forEach((s, index) => {
        const tr = document.createElement("tr");
        tr.innerHTML = `
            <td>${index + 1}</td>
            <td><strong>${escapeHtml(s.name)}</strong></td>
            <td>${escapeHtml(s.registration)}</td>
            <td>${escapeHtml(s.cpf || '-')}</td>
            <td>${s.birth ? formatDate(s.birth) : '-'}</td>
            <td>
                <button class="btn btn-secondary btn-sm" onclick="openStudentModal('${s.id}')">Editar</button>
                <button class="btn btn-danger btn-sm" onclick="handleDeleteStudent('${s.id}')">Excluir</button>
            </td>
        `;
        tbody.appendChild(tr);
    });
}

function openStudentModal(studentId = null) {
    const modal = document.getElementById("modal-student");
    const form = document.getElementById("form-student");

    if (studentId) {
        const c = getCurrentClass();
        const s = c.students.find(item => item.id === studentId);
        document.getElementById("modal-student-title").innerText = "Editar Aluno";
        document.getElementById("student-id").value = s.id;
        document.getElementById("student-name").value = s.name;
        document.getElementById("student-registration").value = s.registration;
        document.getElementById("student-cpf").value = s.cpf || "";
        document.getElementById("student-birth").value = s.birth || "";
    } else {
        document.getElementById("modal-student-title").innerText = "Cadastrar Aluno";
        form.reset();
        document.getElementById("student-id").value = "";
    }

    modal.classList.remove("hidden");
}

function handleSaveStudent(e) {
    e.preventDefault();
    const c = getCurrentClass();
    const id = document.getElementById("student-id").value;
    const name = document.getElementById("student-name").value.trim();
    const registration = document.getElementById("student-registration").value.trim();
    const cpf = document.getElementById("student-cpf").value.trim();
    const birth = document.getElementById("student-birth").value;

    if (id) {
        const s = c.students.find(item => item.id === id);
        s.name = name;
        s.registration = registration;
        s.cpf = cpf;
        s.birth = birth;
    } else {
        const newStudent = {
            id: "al-" + Date.now(),
            name,
            registration,
            cpf,
            birth,
            av1: null,
            av2: null,
            work: null,
            rec: null,
            totalClasses: 20,
            absences: 0,
            dateAttendance: {}
        };
        c.students.push(newStudent);
    }

    Storage.saveClasses(state.classes);
    closeModals();
    renderClassHeader();
    renderCurrentClassTabs();
}

function handleDeleteStudent(studentId) {
    if (confirm("Deseja realmente remover este aluno?")) {
        const c = getCurrentClass();
        c.students = c.students.filter(s => s.id !== studentId);
        Storage.saveClasses(state.classes);
        renderClassHeader();
        renderCurrentClassTabs();
    }
}

// --- ABA NOTAS ---
function renderGradesTable() {
    const c = getCurrentClass();
    const tbody = document.getElementById("table-grades-body");
    tbody.innerHTML = "";

    const students = getFilteredStudents(c.students);

    if (students.length === 0) {
        tbody.innerHTML = `<tr><td colspan="7" class="text-muted">Nenhum aluno cadastrado.</td></tr>`;
        return;
    }

    students.forEach(s => {
        const avg = CONFIG.calculateAverage(s.av1, s.av2, s.work, s.rec);
        const isApproved = avg >= CONFIG.PASSING_GRADE;
        const statusText = isApproved ? "Aprovado" : "Recuperação";
        const statusClass = isApproved ? "text-success" : "text-warning";

        const tr = document.createElement("tr");
        tr.innerHTML = `
            <td><strong>${escapeHtml(s.name)}</strong></td>
            <td><input type="number" step="0.1" min="0" max="10" class="table-input" value="${s.av1 !== null ? s.av1 : ''}" onchange="updateGrade('${s.id}', 'av1', this.value)"></td>
            <td><input type="number" step="0.1" min="0" max="10" class="table-input" value="${s.av2 !== null ? s.av2 : ''}" onchange="updateGrade('${s.id}', 'av2', this.value)"></td>
            <td><input type="number" step="0.1" min="0" max="10" class="table-input" value="${s.work !== null ? s.work : ''}" onchange="updateGrade('${s.id}', 'work', this.value)"></td>
            <td><input type="number" step="0.1" min="0" max="10" class="table-input" value="${s.rec !== null ? s.rec : ''}" onchange="updateGrade('${s.id}', 'rec', this.value)"></td>
            <td><strong>${avg.toFixed(1).replace('.', ',')}</strong></td>
            <td class="${statusClass}"><strong>${statusText}</strong></td>
        `;
        tbody.appendChild(tr);
    });
}

function updateGrade(studentId, field, value) {
    const c = getCurrentClass();
    const s = c.students.find(item => item.id === studentId);
    s[field] = value !== "" ? parseFloat(value) : null;
    Storage.saveClasses(state.classes);
    renderClassHeader();
    renderGradesTable();
}

// --- ABA FREQUÊNCIA ---
function renderAttendanceTable() {
    const c = getCurrentClass();
    const tbody = document.getElementById("table-attendance-body");
    tbody.innerHTML = "";

    const students = getFilteredStudents(c.students);

    if (students.length === 0) {
        tbody.innerHTML = `<tr><td colspan="5" class="text-muted">Nenhum aluno cadastrado.</td></tr>`;
        return;
    }

    students.forEach(s => {
        const total = parseInt(s.totalClasses) || 20;
        const absences = parseInt(s.absences) || 0;
        const presences = Math.max(0, total - absences);
        const freqPercentage = total > 0 ? ((presences / total) * 100).toFixed(0) : 100;

        const tr = document.createElement("tr");
        tr.innerHTML = `
            <td><strong>${escapeHtml(s.name)}</strong></td>
            <td><input type="number" min="1" class="table-input" value="${total}" onchange="updateAttendance('${s.id}', 'totalClasses', this.value)"></td>
            <td>${presences}</td>
            <td><input type="number" min="0" class="table-input" value="${absences}" onchange="updateAttendance('${s.id}', 'absences', this.value)"></td>
            <td><strong>${freqPercentage}%</strong></td>
        `;
        tbody.appendChild(tr);
    });
}

function updateAttendance(studentId, field, value) {
    const c = getCurrentClass();
    const s = c.students.find(item => item.id === studentId);
    s[field] = parseInt(value) || 0;
    Storage.saveClasses(state.classes);
    renderClassHeader();
    renderAttendanceTable();
}

// --- REGISTRO DE FREQUÊNCIA POR DATA ---
function loadDateAttendance() {
    const dateInput = document.getElementById("attendance-date").value;
    if (!dateInput) {
        alert("Por favor, selecione uma data.");
        return;
    }

    const c = getCurrentClass();
    const tbody = document.getElementById("table-date-attendance-body");
    tbody.innerHTML = "";

    c.students.forEach(s => {
        const currentStatus = (s.dateAttendance && s.dateAttendance[dateInput]) ? s.dateAttendance[dateInput] : "P";
        
        const tr = document.createElement("tr");
        tr.innerHTML = `
            <td>${escapeHtml(s.name)}</td>
            <td>
                <select class="date-status-select" data-student-id="${s.id}">
                    <option value="P" ${currentStatus === 'P' ? 'selected' : ''}>Presente</option>
                    <option value="F" ${currentStatus === 'F' ? 'selected' : ''}>Falta</option>
                </select>
            </td>
        `;
        tbody.appendChild(tr);
    });

    document.getElementById("date-attendance-area").classList.remove("hidden");
}

function saveDateAttendance() {
    const dateInput = document.getElementById("attendance-date").value;
    if (!dateInput) return;

    const c = getCurrentClass();
    const selects = document.querySelectorAll(".date-status-select");

    selects.forEach(select => {
        const studentId = select.dataset.studentId;
        const status = select.value;
        const s = c.students.find(item => item.id === studentId);

        if (!s.dateAttendance) s.dateAttendance = {};

        const previousStatus = s.dateAttendance[dateInput];

        // Atualização incremental do total de faltas com base na mudança de presença/falta na data
        if (previousStatus !== status) {
            if (status === 'F' && previousStatus !== 'F') {
                s.absences = (s.absences || 0) + 1;
            } else if (status === 'P' && previousStatus === 'F') {
                s.absences = Math.max(0, (s.absences || 0) - 1);
            }
            s.dateAttendance[dateInput] = status;
        }
    });

    Storage.saveClasses(state.classes);
    alert("Frequência da data salva com sucesso!");
    renderClassHeader();
    renderAttendanceTable();
}

// --- UTILITÁRIOS ---
function closeModals() {
    document.querySelectorAll(".modal").forEach(m => m.classList.add("hidden"));
}

function escapeHtml(text) {
    if (!text) return "";
    return text.toString()
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "&#039;");
}

function formatDate(dateString) {
    if (!dateString) return "";
    const parts = dateString.split("-");
    if (parts.length === 3) {
        return `${parts[2]}/${parts[1]}/${parts[0]}`;
    }
    return dateString;
}
