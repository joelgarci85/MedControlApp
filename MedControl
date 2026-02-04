import React, { useState, useMemo, useEffect } from 'react';
import { 
  LayoutDashboard, Stethoscope, FileCheck, BarChart3, Settings, 
  PlusCircle, Search, Filter, AlertCircle, CheckCircle2, Clock, DollarSign, 
  Menu, X, Upload, ShieldCheck, FileText, Activity, ArrowLeft, 
  AlertTriangle, Send, History, Trash2, Save, Loader2, Download, 
  FileSpreadsheet, LogIn, LogOut, Building, Award, Plus, Edit2, 
  Paperclip, Check, TrendingUp, Bell, Shield, Calendar, List, 
  UserCog, MapPin, CreditCard, Camera, Building2, Landmark, Tag, 
  ScrollText, Sparkles, Wand2, UserPlus, ChevronDown, ChevronRight,
  User as UserIcon, Globe, Database, Users, Eye, FileDown, Copy
} from 'lucide-react';

// --- CONFIGURACIÓN API GEMINI ---
const apiKey = ""; 

const callGemini = async (prompt, systemInstruction = "") => {
  const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
  const payload = {
    contents: [{ parts: [{ text: prompt }] }],
    systemInstruction: { parts: [{ text: systemInstruction }] }
  };

  const fetchWithRetry = async (retries = 5, backoff = 1000) => {
    try {
      const response = await fetch(url, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });
      if (!response.ok) throw new Error(`Error API: ${response.status}`);
      return await response.json();
    } catch (err) {
      if (retries > 0) {
        await new Promise(r => setTimeout(r, backoff));
        return fetchWithRetry(retries - 1, backoff * 2);
      }
      throw err;
    }
  };

  const result = await fetchWithRetry();
  return result.candidates?.[0]?.content?.parts?.[0]?.text || "No se pudo obtener respuesta de la IA.";
};

// --- DATOS INICIALES (MOCKS) ---

const MOCK_PATIENTS = [
  { id: 1, name: 'FIGUEREDO MONZON, DELMIRA', cedula: '1.234.567', phone: '0981 111 222' },
  { id: 2, name: 'ZARATE FRANCO, PAOLA MARIANA', cedula: '2.345.678', phone: '0982 333 444' },
  { id: 3, name: 'MELGAREJO BOGADO, ANA LAURA', cedula: '3.456.789', phone: '0983 555 666' },
  { id: 4, name: 'SOTELO DE BOBADILLA, FABIOLA', cedula: '4.567.890', phone: '0984 777 888' }
];

const MOCK_INSURERS = [
  { id: 1, name: 'ASISMED S.A.', ruc: '80012345-6', status: 'Activo' },
  { id: 2, name: 'SPS', ruc: '80055544-3', status: 'Activo' },
  { id: 3, name: 'MAPFRE', ruc: '80011122-0', status: 'Activo' }
];

const MOCK_SANATORIUMS = [
  { id: 1, name: 'SANATORIO INTERNACIONAL', address: 'Asunción', phone: '021 123 456' },
  { id: 2, name: 'SANATORIO BRITANICO', address: 'Asunción', phone: '021 654 321' }
];

const MOCK_TARIFFS = [
  { id: 1, entityId: 1, entityType: 'insurer', type: 'Consulta', name: 'CONSULTA GINECOLOGICA', amount: 40000, code: '10.01' },
  { id: 2, entityId: 1, entityType: 'insurer', type: 'Procedimiento', name: 'PAP Y COLPOSCOPIA', amount: 44000, code: '20.05' }
];

const INITIAL_ATTENDANCES = [
  { id: 1, patient: 'FIGUEREDO MONZON, DELMIRA', entity: 'ASISMED S.A.', procedure: 'CONSULTA GINECOLOGICA', amount: 40000, date: '2025-07-29', status: 'Conciliado', sanatorium: 'SANATORIO INTERNACIONAL' },
  { id: 2, patient: 'FIGUEREDO MONZON, DELMIRA', entity: 'ASISMED S.A.', procedure: 'PAP Y COLPOSCOPIA', amount: 44000, date: '2025-08-05', status: 'Inconsistente', observation: 'Diferencia arancelaria detectada', sanatorium: 'SANATORIO INTERNACIONAL' },
  { id: 3, patient: 'MELGAREJO BOGADO, ANA LAURA', entity: 'ASISMED S.A.', procedure: 'CONSULTA GINECOLOGICA', amount: 40000, date: '2025-09-04', status: 'Pendiente', sanatorium: 'SANATORIO BRITANICO' },
  { id: 4, patient: 'ZARATE FRANCO, PAOLA MARIANA', entity: 'SANATORIO INTERNACIONAL', procedure: 'HONORARIOS MEDICOS', amount: 77542, date: '2025-06-17', status: 'Conciliado', sanatorium: 'SANATORIO INTERNACIONAL' },
  { id: 5, patient: 'SOTELO DE BOBADILLA, FABIOLA', entity: 'ASISMED S.A.', procedure: 'CONSULTA GINECOLOGICA', amount: 40000, date: '2024-12-26', status: 'Pendiente', sanatorium: 'SANATORIO INTERNACIONAL' }
];

// --- COMPONENTES AUXILIARES ---

const StatusBadge = ({ status }) => {
  const cfg = {
    'Conciliado': 'bg-[#e6f4ea] text-[#1e7e34] border-[#c3e6cb]',
    'Pendiente': 'bg-[#fff4e5] text-[#856404] border-[#ffeeba]',
    'Inconsistente': 'bg-[#fceaea] text-[#721c24] border-[#f5c6cb]'
  };
  return (
    <span className={`px-2 py-0.5 rounded text-[10px] font-bold uppercase border whitespace-nowrap ${cfg[status] || 'bg-gray-100 text-gray-600'}`}>
      {status}
    </span>
  );
};

const KpiCard = ({ title, value, icon, trend, subtitle }) => (
  <div className="bg-white p-4 sm:p-5 rounded-lg border border-slate-200 shadow-sm relative group hover:border-[#0056b3] transition-colors">
     <div className="flex justify-between items-start mb-4">
        <div className="w-10 h-10 rounded-full bg-slate-50 flex items-center justify-center border border-slate-100">{icon}</div>
        {trend && <span className="text-[10px] font-black text-emerald-500 bg-emerald-50 px-1.5 py-0.5 rounded">{trend}</span>}
        {subtitle && <span className="text-[10px] font-bold text-slate-300 uppercase tracking-widest">{subtitle}</span>}
     </div>
     <p className="text-[9px] font-black text-slate-400 uppercase tracking-widest mb-1">{title}</p>
     <h3 className="text-lg font-black text-slate-800 tracking-tight whitespace-nowrap overflow-hidden text-ellipsis">{value}</h3>
  </div>
);

// --- COMPONENTE PRINCIPAL ---

export default function App() {
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  const [activeTab, setActiveTab] = useState('dashboard');
  const [sidebarOpen, setSidebarOpen] = useState(false); // Iniciar cerrado en móvil
  const [loading, setLoading] = useState(null);
  const [toast, setToast] = useState(null);
  const [isMobile, setIsMobile] = useState(false);

  // Estados de Negocio
  const [attendances, setAttendances] = useState(INITIAL_ATTENDANCES);
  const [patients, setPatients] = useState(MOCK_PATIENTS);
  const [insurers, setInsurers] = useState(MOCK_INSURERS);
  const [sanatoriums, setSanatoriums] = useState(MOCK_SANATORIUMS);
  const [tariffs, setTariffs] = useState(MOCK_TARIFFS);
  const [settlementItems, setSettlementItems] = useState([
    { patient: 'FIGUEREDO MONZON, D.', amount: 40000, status: 'Vinculado' },
    { patient: 'FIGUEREDO MONZON, D.', amount: 38500, status: 'Revisar', diff: true, info: 'DIFERENCIA ARANCEL (RECIBIDO < ESPERADO)' },
    { patient: 'ZARATE FRANCO, P.', amount: 77542, status: 'Vinculado' },
    { patient: 'MARTINEZ, MARIA NIDI', amount: 40000, status: 'Revisar', diff: true, info: 'NO EN REGISTROS INTERNOS' }
  ]);

  // Modales
  const [showEntryModal, setShowEntryModal] = useState(false);
  const [showImportModal, setShowImportModal] = useState(false);
  const [entryMode, setEntryMode] = useState('atención');

  // IA Estados
  const [aiText, setAiText] = useState("");
  const [isAiLoading, setIsAiLoading] = useState(false);

  // Responsividad
  useEffect(() => {
    const handleResize = () => {
      const mobile = window.innerWidth < 1024;
      setIsMobile(mobile);
      if (!mobile) setSidebarOpen(true);
      else setSidebarOpen(false);
    };
    handleResize();
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  const notify = (msg) => {
    setToast(msg);
    setTimeout(() => setToast(null), 3000);
  };

  const handleLogin = () => {
    setLoading('Iniciando sesión...');
    setTimeout(() => {
      setIsAuthenticated(true);
      setLoading(null);
      notify("Sesión iniciada correctamente");
    }, 1200);
  };

  const handleAiAnalysis = async () => {
    setIsAiLoading(true);
    const summary = attendances.slice(0, 10).map(a => `${a.entity}: ${a.amount} (${a.status})`).join(', ');
    const prompt = `Analiza la producción médica para la Dra. Nathalia: ${summary}. Resume situación financiera y da 1 consejo. Máximo 2 líneas.`;
    try {
      const result = await callGemini(prompt, "Eres un auditor financiero experto para médicos.");
      setAiText(result);
    } catch (e) {
      notify("Fallo en la conexión con IA");
    } finally {
      setIsAiLoading(false);
    }
  };

  if (!isAuthenticated) return <LoginScreen onLogin={handleLogin} loading={loading} />;

  return (
    <div className="flex flex-col h-screen bg-[#f4f7fa] text-slate-900 overflow-hidden font-sans relative">
      
      {/* Top Bar - Estilo Digitalia */}
      <header className="h-14 bg-[#0056b3] flex items-center justify-between px-4 sm:px-6 shrink-0 z-[60] text-white shadow-md relative">
         <div className="flex items-center gap-3 sm:gap-4">
            <button onClick={() => setSidebarOpen(!sidebarOpen)} className="p-2 hover:bg-white/10 rounded transition-all">
               <Menu size={20} />
            </button>
            <div className="flex items-center gap-2 group cursor-pointer" onClick={() => setActiveTab('dashboard')}>
               <div className="bg-white p-1 rounded-md text-[#0056b3]"><Stethoscope size={20} /></div>
               <h1 className="font-black text-base sm:text-lg tracking-tighter uppercase transition-all">MedControl</h1>
            </div>
         </div>

         <div className="hidden lg:flex flex-1 max-w-xl mx-10 relative">
            <Search size={16} className="absolute left-3 top-1/2 -translate-y-1/2 text-white/50" />
            <input 
               placeholder="Buscar: Pacientes, Liquidaciones, Registros..." 
               className="w-full bg-white/10 border-none rounded-md py-1.5 pl-10 pr-4 text-xs placeholder:text-white/40 focus:bg-white/20 outline-none transition-all"
            />
         </div>

         <div className="flex items-center gap-3 sm:gap-6">
            <div className="hidden sm:block">
               <Bell size={18} className="text-white/80 cursor-pointer hover:text-white" />
            </div>
            <div className="flex items-center gap-3 group cursor-pointer">
               <div className="text-right hidden lg:block">
                  <p className="text-[10px] font-black leading-none mb-1 uppercase tracking-tighter">DRA. NATHALIA QUEVEDO</p>
                  <p className="text-[9px] text-white/60 font-bold uppercase tracking-widest leading-none">SANATORIO INTERNACIONAL</p>
               </div>
               <div className="w-8 h-8 sm:w-9 sm:h-9 rounded-full bg-white/20 border border-white/20 flex items-center justify-center text-white font-black text-xs">NQ</div>
            </div>
         </div>
      </header>

      <div className="flex flex-1 overflow-hidden relative">
        {/* Sidebar - Menú Lateral con Overlay en móvil */}
        {isMobile && sidebarOpen && (
          <div 
            className="fixed inset-0 bg-black/50 z-40 transition-opacity"
            onClick={() => setSidebarOpen(false)}
          />
        )}
        <aside className={`
          ${isMobile ? 'fixed inset-y-0 left-0 z-[50] shadow-2xl pt-14' : 'relative'}
          ${sidebarOpen ? 'w-64 translate-x-0' : 'w-0 -translate-x-full overflow-hidden'}
          bg-white border-r border-slate-200 transition-all duration-300 flex flex-col shrink-0
        `}>
          <div className="flex-1 py-6 overflow-y-auto custom-scrollbar">
             <NavSection title="TABLERO" collapsed={!sidebarOpen}>
                <SidebarItem icon={<LayoutDashboard size={18}/>} label="Dashboard" active={activeTab === 'dashboard'} onClick={() => { setActiveTab('dashboard'); if(isMobile) setSidebarOpen(false); }} />
                <SidebarItem icon={<BarChart3 size={18}/>} label="Reportes" active={activeTab === 'reports'} onClick={() => { setActiveTab('reports'); if(isMobile) setSidebarOpen(false); }} />
             </NavSection>

             <NavSection title="OPERACIONES" collapsed={!sidebarOpen}>
                <SidebarItem icon={<PlusCircle size={18}/>} label="Atenciones" active={activeTab === 'attendances'} onClick={() => { setActiveTab('attendances'); if(isMobile) setSidebarOpen(false); }} />
                <SidebarItem icon={<FileCheck size={18}/>} label="Conciliación" active={activeTab === 'conciliation'} onClick={() => { setActiveTab('conciliation'); if(isMobile) setSidebarOpen(false); }} badge={attendances.filter(a => a.status === 'Pendiente').length} />
                <SidebarItem icon={<History size={18}/>} label="Trazabilidad" active={activeTab === 'audit'} onClick={() => { setActiveTab('audit'); if(isMobile) setSidebarOpen(false); }} />
             </NavSection>

             <NavSection title="MAESTROS" collapsed={!sidebarOpen}>
                <SidebarItem icon={<Building size={18}/>} label="Sanatorios" active={activeTab === 'sanatoriums'} onClick={() => { setActiveTab('sanatoriums'); if(isMobile) setSidebarOpen(false); }} />
                <SidebarItem icon={<Landmark size={18}/>} label="Aseguradoras" active={activeTab === 'insurers'} onClick={() => { setActiveTab('insurers'); if(isMobile) setSidebarOpen(false); }} />
                <SidebarItem icon={<Tag size={18}/>} label="Tarifas / Aranceles" active={activeTab === 'tariffs'} onClick={() => { setActiveTab('tariffs'); if(isMobile) setSidebarOpen(false); }} />
                <SidebarItem icon={<Users size={18}/>} label="Pacientes" active={activeTab === 'patients'} onClick={() => { setActiveTab('patients'); if(isMobile) setSidebarOpen(false); }} />
             </NavSection>

             <NavSection title="AJUSTES" collapsed={!sidebarOpen}>
                <SidebarItem icon={<UserCog size={18}/>} label="Perfil" active={activeTab === 'settings'} onClick={() => { setActiveTab('settings'); if(isMobile) setSidebarOpen(false); }} />
             </NavSection>
          </div>
          <div className="p-4 border-t border-slate-100 bg-slate-50/50">
             <button onClick={() => setIsAuthenticated(false)} className="w-full flex items-center gap-3 px-4 py-3 rounded-lg text-rose-500 hover:bg-rose-50 transition-all font-bold text-[10px] uppercase tracking-widest">
                <LogOut size={16} /> Salir
             </button>
          </div>
        </aside>

        {/* Área de Contenido */}
        <main className="flex-1 overflow-auto bg-[#f4f7fa] p-4 sm:p-6 custom-scrollbar">
           {activeTab === 'dashboard' && (
             <DashboardView 
               attendances={attendances} 
               onAiAnalysis={handleAiAnalysis} 
               aiText={aiText} 
               isAiLoading={isAiLoading} 
               onNewEntry={() => {setEntryMode('atención'); setShowEntryModal(true);}}
             />
           )}
           {activeTab === 'conciliation' && (
             <ConciliacionModule 
               internal={attendances} 
               external={settlementItems} 
               onImport={() => setShowImportModal(true)}
               onConciliate={(id) => {
                 setAttendances(prev => prev.map(a => a.id === id ? {...a, status: 'Conciliado'} : a));
                 notify("Registro conciliado correctamente");
               }}
               onInconsistency={(id, obs) => {
                 setAttendances(prev => prev.map(a => a.id === id ? {...a, status: 'Inconsistente', observation: obs} : a));
                 notify("Inconsistencia registrada");
               }}
             />
           )}
           {/* Otros componentes */}
           {activeTab === 'attendances' && <AtencionesModule data={attendances} onNew={() => setShowEntryModal(true)} notify={notify} />}
           {activeTab === 'reports' && <ReportesModule data={attendances} notify={notify} />}
           {activeTab === 'tariffs' && <TarifasModule tariffs={tariffs} insurers={insurers} notify={notify} />}
           {activeTab === 'patients' && <PacientesModule patients={patients} notify={notify} />}
           {activeTab === 'insurers' && <EntidadesModule items={insurers} type="Aseguradora" notify={notify} />}
           {activeTab === 'sanatoriums' && <EntidadesModule items={sanatoriums} type="Sanatorio" notify={notify} />}
           {activeTab === 'settings' && <SettingsModule notify={notify} />}
           {activeTab === 'audit' && <AuditModule notify={notify} />}
        </main>
      </div>

      {/* Modales - Responsivos con max-w-[90vw] */}
      <Modal isOpen={showEntryModal} onClose={() => setShowEntryModal(false)} title={`Registrar ${entryMode}`}>
        <AtencionForm insurers={insurers} patients={patients} sanatoriums={sanatoriums} onSave={(row) => { setAttendances([row, ...attendances]); setShowEntryModal(false); notify("Atención guardada correctamente"); }} />
      </Modal>

      <Modal isOpen={showImportModal} onClose={() => setShowImportModal(false)} title="Importar Liquidación Externa">
        <ImportForm onImport={(items) => { setSettlementItems(items); setShowImportModal(false); notify("Liquidación cargada correctamente"); }} />
      </Modal>

      {/* Toast de Notificación */}
      {toast && (
        <div className="fixed bottom-4 sm:bottom-8 right-4 sm:right-8 bg-slate-900 text-white px-6 py-3 rounded-md shadow-2xl flex items-center gap-3 z-[110] animate-in slide-in-from-right-4 border-l-4 border-teal-500 max-w-[80vw]">
          <CheckCircle2 size={16} className="text-teal-400 shrink-0" />
          <span className="text-[10px] font-black uppercase tracking-widest">{toast}</span>
        </div>
      )}
    </div>
  );
}

// --- VISTAS DEL DASHBOARD ---

const DashboardView = ({ attendances, onAiAnalysis, aiText, isAiLoading, onNewEntry }) => {
  return (
    <div className="space-y-6">
      <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
        <KpiCard title="Pendiente de Cobro" value={`Gs. 3.125.400`} icon={<Clock className="text-amber-500"/>} trend="+15%" />
        <KpiCard title="Liquidado este Mes" value={`Gs. 8.450.000`} icon={<CheckCircle2 className="text-emerald-500"/>} trend="+2%" />
        <KpiCard title="Diferencias Detectadas" value={`5 Alertas`} icon={<AlertTriangle className="text-rose-500"/>} subtitle="Revisión req." />
        <div className="bg-[#0056b3] p-4 sm:p-5 rounded-lg text-white shadow-sm flex flex-col justify-between">
           <h4 className="text-[10px] font-black uppercase tracking-widest opacity-80">Auditoría Gemini</h4>
           <button onClick={onAiAnalysis} disabled={isAiLoading} className="mt-4 bg-white/20 hover:bg-white/30 text-white py-2 rounded font-black text-[9px] uppercase tracking-widest transition-all">
              {isAiLoading ? <Loader2 className="animate-spin inline mr-2" size={14}/> : <Sparkles size={14} className="inline mr-2"/>}
              Analizar Cartera
           </button>
        </div>
      </div>

      <div className="grid grid-cols-1 xl:grid-cols-3 gap-6">
        <div className="xl:col-span-2 bg-white rounded-lg border border-slate-200 shadow-sm overflow-hidden">
           <div className="px-4 sm:px-6 py-4 border-b border-slate-100 flex flex-col sm:flex-row justify-between bg-white items-center gap-2">
              <h3 className="text-sm font-black uppercase tracking-tight text-slate-800">Atenciones Recientes</h3>
              <button className="text-[9px] font-black text-[#0056b3] uppercase hover:underline flex items-center gap-1 border border-slate-200 px-3 py-1.5 rounded-lg w-full sm:w-auto justify-center">
                 <FileDown size={14}/> Exportar Excel
              </button>
           </div>
           <div className="overflow-x-auto">
              <table className="w-full text-left text-[11px] min-w-[500px]">
                 <thead className="bg-[#f8f9fa] text-slate-400 font-black uppercase tracking-widest">
                    <tr className="border-b border-slate-100">
                       <th className="px-6 py-4">Paciente / Seguro</th>
                       <th className="px-6 py-4">Servicio</th>
                       <th className="px-6 py-4 text-right">Importe</th>
                       <th className="px-6 py-4 text-center">Estado</th>
                    </tr>
                 </thead>
                 <tbody className="divide-y divide-slate-100">
                    {attendances.slice(0, 5).map(a => (
                      <tr key={a.id} className="hover:bg-slate-50 transition-colors">
                        <td className="px-6 py-5 font-bold text-slate-800 uppercase leading-none">
                           {a.patient}
                           <div className="text-[9px] text-slate-400 mt-1">{a.entity}</div>
                        </td>
                        <td className="px-6 py-5">
                           <div className="flex items-center gap-2">
                              <span className={`px-2 py-0.5 rounded text-[8px] font-black uppercase whitespace-nowrap ${a.procedure.includes('CONSULTA') ? 'bg-blue-50 text-blue-500' : 'bg-purple-50 text-purple-500'}`}>
                                 {a.procedure.includes('CONSULTA') ? 'Consulta' : 'Procedimiento'}
                              </span>
                           </div>
                        </td>
                        <td className="px-6 py-5 text-right font-black text-slate-900 text-xs whitespace-nowrap">Gs. {a.amount.toLocaleString()}</td>
                        <td className="px-6 py-5 text-center"><StatusBadge status={a.status} /></td>
                      </tr>
                    ))}
                 </tbody>
              </table>
           </div>
        </div>
        <div className="space-y-6">
           <div className="bg-[#0b514b] p-6 sm:p-8 rounded-[1.5rem] text-white shadow-xl relative overflow-hidden">
              <h4 className="text-xl font-black uppercase tracking-tighter mb-1">Carga Express</h4>
              <p className="text-[10px] font-bold opacity-70 uppercase tracking-widest mb-10 italic">Registra tus honorarios inmediatamente.</p>
              <button onClick={onNewEntry} className="w-full bg-white text-[#0b514b] py-3.5 rounded-full font-black text-[11px] uppercase tracking-widest shadow-lg hover:bg-slate-50 transition-all">NUEVA ATENCIÓN</button>
           </div>
        </div>
      </div>
    </div>
  );
};

// --- MÓDULO DE CONCILIACIÓN ---

const ConciliacionModule = ({ internal, external, onImport, onConciliate, onInconsistency }) => {
  const [selectedInternal, setSelectedInternal] = useState(null);

  return (
    <div className="space-y-6 animate-in fade-in duration-500">
       {/* Cabecera de Conciliación - Stack en móvil */}
       <div className="bg-white p-6 sm:p-10 rounded-[1.5rem] sm:rounded-[2.5rem] border border-slate-100 shadow-xl flex flex-col md:flex-row justify-between items-center relative overflow-hidden gap-6">
          <div className="absolute top-0 left-0 w-2 md:h-full bg-[#0056b3] h-2 w-full md:w-2"></div>
          <div className="flex items-center gap-4 sm:gap-6 self-start md:self-center">
             <div className="w-12 h-12 sm:w-16 sm:h-16 bg-blue-50 text-[#0056b3] rounded-2xl flex items-center justify-center shadow-inner ring-4 ring-white shrink-0">
                <ShieldCheck size={28} className="sm:size-[32px]" />
             </div>
             <div>
                <h3 className="text-xl sm:text-2xl font-black text-slate-800 uppercase tracking-tighter leading-none mb-2">CONCILIACIÓN ASISTIDA</h3>
                <p className="text-[9px] sm:text-[10px] font-black text-slate-400 uppercase tracking-[0.2em] sm:tracking-[0.3em] leading-none italic opacity-60">CARTERA VS. LIQUIDACIONES</p>
             </div>
          </div>
          <div className="flex flex-col sm:flex-row gap-3 w-full md:w-auto">
             <button className="px-6 py-3 bg-white border border-slate-200 text-slate-600 rounded-lg text-[10px] font-black uppercase tracking-widest hover:bg-slate-50 transition-all">EXPORTAR</button>
             <button onClick={onImport} className="bg-[#121926] text-white px-8 py-3 rounded-lg text-[10px] font-black uppercase tracking-widest flex items-center justify-center gap-2 shadow-xl hover:bg-slate-800 transition-all">
                <Upload size={16}/> SUBIR LIQUIDACIÓN
             </button>
          </div>
       </div>

       <div className="grid grid-cols-1 lg:grid-cols-2 gap-6 sm:gap-10">
          {/* MIS REGISTROS */}
          <div className="space-y-4">
             <h4 className="text-[10px] font-black text-slate-400 uppercase tracking-widest flex items-center gap-2 ml-4">
                <div className="w-2 h-2 bg-teal-400 rounded-full animate-pulse"></div> MIS REGISTROS
             </h4>
             <div className="bg-white rounded-[1.5rem] sm:rounded-[2rem] border border-slate-100 shadow-sm h-[400px] sm:h-[600px] overflow-y-auto divide-y divide-slate-50 custom-scrollbar">
                {internal.map(a => (
                  <div 
                    key={a.id} 
                    className={`p-4 sm:p-6 transition-all cursor-pointer flex justify-between items-center group relative ${selectedInternal === a.id ? 'bg-blue-50/50 shadow-inner' : 'hover:bg-slate-50/50'}`}
                    onClick={() => setSelectedInternal(a.id)}
                  >
                     {selectedInternal === a.id && <div className="absolute left-0 top-0 w-1 h-full bg-[#0056b3]"></div>}
                     <div className="flex items-center gap-3 sm:gap-4 overflow-hidden">
                        <div className={`w-1 h-10 sm:h-12 rounded-full shrink-0 ${a.status === 'Conciliado' ? 'bg-emerald-400' : a.status === 'Inconsistente' ? 'bg-rose-400' : 'bg-amber-400'}`}></div>
                        <div className="overflow-hidden">
                           <p className="text-xs font-black text-[#0056b3] uppercase leading-none mb-1 sm:mb-2 tracking-tight truncate">{a.patient}</p>
                           <p className="text-[8px] sm:text-[9px] font-bold text-slate-400 uppercase leading-none tracking-widest truncate">{a.date} • {a.procedure}</p>
                        </div>
                     </div>
                     <div className="text-right shrink-0">
                        <p className="text-xs sm:text-sm font-black text-slate-900 font-mono tracking-tighter">Gs. {a.amount.toLocaleString()}</p>
                        <p className={`text-[8px] sm:text-[9px] font-black uppercase mt-1 tracking-widest ${a.status === 'Conciliado' ? 'text-emerald-500' : a.status === 'Inconsistente' ? 'text-rose-500' : 'text-amber-500'}`}>
                           {a.status}
                        </p>
                     </div>
                  </div>
                ))}
             </div>
          </div>

          {/* LIQUIDACIÓN RECIBIDA */}
          <div className="space-y-4">
             <h4 className="text-[10px] font-black text-[#0056b3] uppercase tracking-widest flex items-center gap-2 ml-4">
                <ScrollText size={16}/> LIQUIDACIÓN RECIBIDA
             </h4>
             <div className="bg-[#f8f9fc] rounded-[1.5rem] sm:rounded-[2rem] border border-slate-200 shadow-inner h-[400px] sm:h-[600px] overflow-y-auto divide-y divide-slate-100 custom-scrollbar">
                {external.length === 0 ? (
                  <div className="h-full flex flex-col items-center justify-center p-10 sm:p-20 text-center">
                     <FileSpreadsheet size={48} className="text-slate-200 mb-6"/>
                     <p className="text-[10px] font-black text-slate-300 uppercase tracking-[0.3em] leading-relaxed max-w-[200px]">Suba el extracto de liquidación aquí.</p>
                  </div>
                ) : (
                  external.map((e, idx) => (
                    <div key={idx} className={`p-6 sm:p-8 flex flex-col sm:flex-row items-start sm:items-center justify-between group transition-all bg-white gap-4`}>
                       <div className="flex items-center gap-4 sm:gap-6 overflow-hidden">
                          <div className={`w-10 h-10 sm:w-14 sm:h-14 rounded-2xl flex items-center justify-center shadow-inner shrink-0 ${e.diff ? 'bg-rose-100 text-rose-500' : 'bg-emerald-50 text-emerald-500'}`}>
                             {e.diff ? <AlertTriangle size={20} /> : <CheckCircle2 size={20} />}
                          </div>
                          <div className="overflow-hidden">
                             <p className="text-xs sm:text-sm font-black text-slate-800 uppercase mb-1 sm:mb-2 tracking-tighter truncate">{e.patient}</p>
                             <p className={`text-[8px] sm:text-[10px] font-black uppercase tracking-widest leading-none ${e.diff ? 'text-rose-500' : 'text-emerald-500'} truncate`}>
                                {e.info || 'LIQUIDADO 100%'}
                             </p>
                          </div>
                       </div>
                       <div className="text-right flex flex-row sm:flex-col items-center sm:items-end gap-3 w-full sm:w-auto justify-between sm:justify-center">
                          <p className="text-xs sm:text-sm font-black text-slate-900 font-mono tracking-tighter">Gs. {e.amount.toLocaleString()}</p>
                          <button 
                            onClick={() => e.status === 'Vinculado' ? onConciliate(selectedInternal) : onInconsistency(selectedInternal, e.info)}
                            className={`text-[8px] sm:text-[9px] font-black uppercase px-4 py-2 rounded shadow-lg transition-all ${e.status === 'Vinculado' ? 'bg-emerald-500 text-white' : 'bg-[#121926] text-white'} w-auto sm:w-full`}
                          >
                             {e.status === 'Vinculado' ? 'CONCILIAR' : 'REVISAR'}
                          </button>
                       </div>
                    </div>
                  ))
                )}
             </div>
          </div>
       </div>
    </div>
  );
};

// --- ESTRUCTURA DE COMPONENTES ---

const Modal = ({ isOpen, onClose, title, children }) => {
  if (!isOpen) return null;
  return (
    <div className="fixed inset-0 z-[100] flex items-center justify-center p-4">
       <div className="absolute inset-0 bg-slate-900/60 backdrop-blur-sm" onClick={onClose}></div>
       <div className="bg-white w-full max-w-xl rounded-2xl shadow-2xl relative z-10 overflow-hidden animate-in zoom-in-95 duration-200">
          <div className="px-4 sm:px-6 py-4 border-b border-slate-100 flex justify-between items-center bg-[#f8f9fa]">
             <h3 className="text-xs font-black uppercase text-slate-700 tracking-tight truncate">{title}</h3>
             <button onClick={onClose} className="p-1 hover:bg-slate-200 rounded text-slate-400"><X size={20}/></button>
          </div>
          <div className="p-4 sm:p-8 max-h-[85vh] overflow-y-auto custom-scrollbar">{children}</div>
       </div>
    </div>
  );
};

const AtencionForm = ({ insurers, patients, sanatoriums, onSave }) => {
  const [formData, setFormData] = useState({ date: new Date().toISOString().split('T')[0], patientId: '', entity: insurers[0]?.name, sanatorium: sanatoriums[0]?.name, amount: 40000, procedure: 'CONSULTA GINECOLOGICA' });
  return (
    <form className="space-y-4 sm:space-y-6" onSubmit={(e) => { e.preventDefault(); const p = patients.find(pat => pat.id === parseInt(formData.patientId)); onSave({ ...formData, id: Date.now(), patient: p ? p.name : 'PACIENTE NUEVO', status: 'Pendiente' }); }}>
       <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <div className="space-y-2"><label className="text-[10px] font-black uppercase tracking-widest text-slate-400 ml-1">Fecha</label><input type="date" className="w-full px-4 py-3 bg-[#f8f9fa] border border-slate-200 rounded-lg text-xs font-bold outline-none" value={formData.date} onChange={e => setFormData({...formData, date: e.target.value})}/></div>
          <div className="space-y-2"><label className="text-[10px] font-black uppercase tracking-widest text-slate-400 ml-1">Sanatorio</label><select className="w-full px-4 py-3 bg-[#f8f9fa] border border-slate-200 rounded-lg text-xs font-bold appearance-none outline-none" value={formData.sanatorium} onChange={e => setFormData({...formData, sanatorium: e.target.value})}>{sanatoriums.map(s => <option key={s.id}>{s.name}</option>)}</select></div>
       </div>
       <div className="space-y-2"><label className="text-[10px] font-black uppercase tracking-widest text-slate-400 ml-1">Paciente</label><select className="w-full px-4 py-3 bg-[#f8f9fa] border border-slate-200 rounded-lg text-xs font-bold outline-none" value={formData.patientId} onChange={e => setFormData({...formData, patientId: e.target.value})}><option value="">Seleccionar paciente...</option>{patients.map(p => <option key={p.id} value={p.id}>{p.name}</option>)}</select></div>
       <div className="pt-4 sm:pt-6"><button type="submit" className="w-full bg-[#0056b3] text-white py-4 sm:py-5 rounded-xl font-black uppercase text-[10px] sm:text-[11px] tracking-[0.2em] shadow-xl">GUARDAR REGISTRO</button></div>
    </form>
  );
};

const ImportForm = ({ onImport }) => (
  <div className="space-y-6 sm:space-y-8 p-2">
     <div className="p-8 sm:p-16 border-2 border-dashed border-slate-200 rounded-[1.5rem] sm:rounded-[2rem] text-center bg-[#f8f9fc] hover:bg-slate-50 transition-all cursor-pointer">
        <FileSpreadsheet size={48} className="mx-auto text-slate-300 mb-6 sm:size-[64px]"/>
        <p className="text-[10px] sm:text-[11px] font-black uppercase tracking-[0.2em] text-slate-400">Importar liquidación (.XLSX, .CSV)</p>
     </div>
     <button onClick={() => onImport([
        { patient: 'FIGUEREDO MONZON, D.', amount: 40000, status: 'Vinculado' },
        { patient: 'FIGUEREDO MONZON, D.', amount: 38500, status: 'Revisar', diff: true, info: 'DIFERENCIA ARANCEL (RECIBIDO < ESPERADO)' }
     ])} className="w-full bg-[#121926] text-white py-4 sm:py-5 rounded-xl font-black uppercase text-[10px] sm:text-[11px] tracking-widest shadow-2xl">INICIAR PROCESAMIENTO</button>
  </div>
);

// --- COMPONENTES DE VISTA ---
const AtencionesModule = ({ data, onNew }) => ( <div className="bg-white rounded-lg border border-slate-200 shadow-sm overflow-hidden"><div className="px-6 py-4 border-b border-slate-100 flex flex-col sm:flex-row justify-between bg-white items-center gap-3"><h3 className="text-sm font-black uppercase tracking-tight text-slate-800">Atenciones</h3><button onClick={onNew} className="w-full sm:w-auto bg-[#0056b3] text-white px-5 py-2 rounded-lg text-[10px] font-black uppercase tracking-widest flex items-center justify-center gap-2 shadow-lg"><Plus size={16}/> Nueva Atención</button></div><div className="overflow-x-auto"><table className="w-full text-left text-[11px] min-w-[500px]"><thead className="bg-[#f8f9fa] text-slate-400 font-black uppercase tracking-widest border-b"><tr><th className="px-6 py-4">Fecha</th><th className="px-6 py-4">Paciente</th><th className="px-6 py-4 text-right">Importe</th><th className="px-6 py-4 text-center">Estado</th></tr></thead><tbody className="divide-y divide-slate-100">{data.map(a => (<tr key={a.id} className="hover:bg-slate-50 text-[11px]"><td className="px-6 py-4 text-slate-400 font-bold whitespace-nowrap">{a.date}</td><td className="px-6 py-4 font-bold text-slate-800 uppercase leading-tight">{a.patient}</td><td className="px-6 py-4 font-black text-slate-900 text-right whitespace-nowrap">Gs. {a.amount.toLocaleString()}</td><td className="px-6 py-4 text-center"><StatusBadge status={a.status} /></td></tr>))}</tbody></table></div></div> );
const TarifasModule = ({ tariffs }) => ( <div className="bg-white rounded-lg border border-slate-200 shadow-sm overflow-hidden"><div className="px-6 py-4 border-b bg-[#f8f9fa] flex flex-col sm:flex-row justify-between items-center gap-2"><h3 className="text-xs font-black uppercase text-slate-700 tracking-tight">Catálogo de Aranceles</h3><button className="text-[#0056b3] text-[10px] font-black uppercase flex items-center gap-1 hover:underline"><Plus size={14}/> Nuevo Arancel</button></div><div className="overflow-x-auto"><table className="w-full text-left text-[11px] min-w-[400px]"><thead className="bg-[#f8f9fa] text-slate-400 font-black uppercase tracking-widest border-b"><tr><th className="px-6 py-4">Concepto</th><th className="px-6 py-4 text-right">Monto Pactado</th></tr></thead><tbody className="divide-y divide-slate-100 font-bold">{tariffs.map(t => (<tr key={t.id} className="hover:bg-slate-50"><td className="px-6 py-5 uppercase text-slate-800 leading-tight">{t.name}</td><td className="px-6 py-5 text-right font-black text-[#0056b3] whitespace-nowrap">Gs. {t.amount.toLocaleString()}</td></tr>))}</tbody></table></div></div> );
const PacientesModule = ({ patients }) => ( <div className="bg-white rounded-lg border border-slate-200 shadow-sm overflow-hidden"><div className="px-6 py-4 border-b bg-[#f8f9fa] flex flex-col sm:flex-row justify-between items-center gap-2"><h3 className="text-xs font-black uppercase text-slate-700 tracking-tight">Directorio de Pacientes</h3></div><div className="overflow-x-auto"><table className="w-full text-left text-[11px] min-w-[350px]"><thead className="bg-[#f8f9fa] text-slate-400 font-black uppercase tracking-widest border-b"><tr><th className="px-6 py-4">Nombre</th><th className="px-6 py-4">Documento</th></tr></thead><tbody className="divide-y divide-slate-100 font-bold">{patients.map(p => (<tr key={p.id} className="hover:bg-slate-50"><td className="px-6 py-5 uppercase text-slate-800">{p.name}</td><td className="px-6 py-5 text-slate-400 font-mono whitespace-nowrap">{p.cedula}</td></tr>))}</tbody></table></div></div> );
const EntidadesModule = ({ items, type }) => ( <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4 sm:gap-6 animate-in slide-in-from-bottom-4">{items.map(i => (<div key={i.id} className="bg-white p-6 rounded-xl border border-slate-200 shadow-sm hover:border-[#0056b3] transition-all group"><div className="flex items-center gap-4 mb-4"><div className="w-12 h-12 bg-[#f8f9fa] rounded-lg flex items-center justify-center text-[#0056b3] shadow-inner">{type === 'Sanatorio' ? <Building size={24}/> : <Landmark size={24}/>}</div><div><h4 className="text-xs font-black uppercase text-slate-800 tracking-tight leading-tight">{i.name}</h4><p className="text-[9px] font-black text-slate-400 uppercase tracking-widest italic">{type}</p></div></div><div className="mt-8 flex gap-2"><button className="flex-1 bg-slate-50 py-2.5 rounded-lg text-[10px] font-black uppercase text-slate-500 hover:bg-slate-100">Configurar</button></div></div>))}</div> );
const SettingsModule = () => ( <div className="max-w-2xl mx-auto space-y-6 sm:space-y-10 animate-in slide-in-from-bottom-8"><div className="bg-white rounded-3xl border border-slate-100 shadow-xl overflow-hidden"><div className="h-24 sm:h-32 bg-[#0056b3] relative"><div className="absolute -bottom-8 sm:-bottom-12 left-6 sm:left-12 w-20 sm:w-28 h-20 sm:h-28 bg-white p-2 rounded-2xl shadow-xl"><div className="w-full h-full bg-[#f8f9fa] rounded-xl flex items-center justify-center text-[#0056b3] text-2xl sm:text-4xl font-black shadow-inner">NQ</div></div></div><div className="pt-12 sm:pt-16 px-6 sm:px-12 pb-8 sm:pb-12 space-y-6 sm:space-y-10"><div><h3 className="text-xl sm:text-2xl font-black text-slate-800 uppercase tracking-tighter leading-none mb-2">Dra. Nathalia Quevedo</h3><p className="text-[10px] font-black text-slate-400 uppercase tracking-[0.4em] italic leading-none">Médico Profesional</p></div></div></div></div> );
const ReportesModule = () => ( <div className="bg-white p-6 sm:p-12 rounded-[2rem] sm:rounded-[3rem] border border-slate-100 shadow-sm"><h3 className="text-2xl sm:text-3xl font-black text-slate-800 uppercase tracking-tighter leading-none mb-2">Informes de Producción</h3><div className="flex flex-col sm:flex-row gap-4 mt-8"><button className="w-full sm:px-10 py-4 bg-[#f8f9fa] border border-slate-200 rounded-xl text-[10px] font-black uppercase tracking-widest shadow-sm">Descargar PDF</button><button className="w-full sm:px-10 py-4 bg-[#1e7e34] text-white rounded-xl text-[10px] font-black uppercase tracking-widest shadow-xl">Excel .XLSX</button></div></div> );
const AuditModule = () => ( <div className="bg-white rounded-lg border border-slate-200 shadow-sm overflow-hidden animate-in slide-in-from-bottom-4"><div className="px-6 py-4 border-b bg-[#f8f9fa]"><h3 className="text-xs font-black uppercase text-slate-700 tracking-tight">Historial de Auditoría</h3></div><div className="divide-y divide-slate-100 p-6 text-[10px] text-slate-400 font-bold uppercase text-center italic">No hay registros recientes para mostrar en esta vista.</div></div> );

const NavSection = ({ title, children, collapsed }) => ( <div className="mb-6">{!collapsed && <p className="px-5 text-[9px] font-black text-slate-400 uppercase tracking-[0.2em] mb-3">{title}</p>}<div className="space-y-0.5">{children}</div></div> );
const SidebarItem = ({ icon, label, active, onClick, collapsed, badge }) => ( <button onClick={onClick} className={`w-full flex items-center justify-between px-5 py-2.5 transition-all relative ${active ? 'bg-[#0056b3]/5 text-[#0056b3] border-r-4 border-[#0056b3] font-black' : 'text-slate-500 hover:bg-slate-50 hover:text-slate-800'}`}><div className="flex items-center gap-3 shrink-0"><span className={`${active ? 'text-[#0056b3]' : 'text-slate-400'}`}>{icon}</span>{label && <span className="text-[11px] uppercase tracking-wide truncate">{label}</span>}</div>{!collapsed && badge > 0 && ( <span className="text-[9px] font-black px-1.5 py-0.5 bg-[#0056b3] text-white rounded-full shadow-lg shrink-0">{badge}</span> )}</button> );

const LoginScreen = ({ onLogin, loading }) => (
  <div className="min-h-screen bg-[#0056b3] flex items-center justify-center p-4 sm:p-6 relative overflow-hidden font-sans">
     <div className="absolute top-[-15%] left-[-15%] w-[400px] sm:w-[600px] h-[400px] sm:h-[600px] bg-white/5 rounded-full blur-[120px]"></div>
     <div className="absolute bottom-[-15%] right-[-15%] w-[400px] sm:w-[600px] h-[400px] sm:h-[600px] bg-white/5 rounded-full blur-[120px]"></div>
     <div className="w-full max-w-sm bg-white rounded-3xl shadow-[0_20px_60px_-15px_rgba(0,0,0,0.3)] p-8 sm:p-12 text-center relative z-10">
        <div className="w-14 h-14 sm:w-16 sm:h-16 bg-[#0056b3] rounded-xl flex items-center justify-center text-white mx-auto mb-8 sm:mb-10 shadow-[0_10px_20px_-5px_rgba(0,86,179,0.4)]">
           <Stethoscope size={32} />
        </div>
        <h1 className="text-2xl sm:text-3xl font-black text-slate-800 uppercase tracking-tighter mb-1">MEDCONTROL</h1>
        <p className="text-slate-400 text-[9px] sm:text-[10px] font-black uppercase tracking-[0.3em] mb-10 sm:mb-14">GESTIÓN MÉDICA PROFESIONAL</p>
        <div className="space-y-4 sm:space-y-6 text-left">
           <div className="space-y-2"><label className="text-[9px] sm:text-[10px] font-black text-slate-400 uppercase tracking-widest ml-1">USUARIO / GMAIL</label><input className="w-full px-4 sm:px-5 py-3 sm:py-4 bg-[#f8f9fa] border border-slate-200 rounded-lg font-bold text-xs outline-none" placeholder="ej: nquevedo@gmail.com" /></div>
           <div className="space-y-2"><label className="text-[9px] sm:text-[10px] font-black text-slate-400 uppercase tracking-widest ml-1">CONTRASEÑA</label><input type="password" placeholder="••••••••" className="w-full px-4 sm:px-5 py-3 sm:py-4 bg-[#f8f9fa] border border-slate-200 rounded-lg font-bold text-xs outline-none" /></div>
           <button onClick={onLogin} disabled={!!loading} className="w-full bg-[#0056b3] text-white font-black py-4 rounded-lg shadow-lg uppercase tracking-widest text-[10px] mt-6 flex items-center justify-center gap-3">{loading ? <Loader2 className="animate-spin" size={18}/> : 'INICIAR SESIÓN'}</button>
        </div>
        <button onClick={onLogin} className="w-full bg-white border border-slate-200 text-slate-500 font-black py-3.5 rounded-lg mt-6 sm:mt-8 uppercase tracking-widest text-[9px] sm:text-[10px] flex items-center justify-center gap-3 shadow-sm">
          <img src="https://www.gstatic.com/images/branding/product/1x/gsa_512dp.png" className="w-4 h-4" alt="Google" /> GMAIL CORPORATIVO
        </button>
     </div>
  </div>
);
