import React, { useState, useMemo } from 'react';
import { 
  LayoutDashboard, 
  Users, 
  HandCoins, 
  Church, 
  Calendar, 
  Plus, 
  Search, 
  MoreVertical,
  TrendingUp,
  UserPlus,
  ArrowUpCircle,
  ArrowDownCircle,
  Menu,
  X,
  ChevronRight
} from 'lucide-react';

// --- Mock Data ---
const INITIAL_MEMBERS = [
  { id: 1, name: 'João Silva', role: 'Pastor', status: 'Ativo', baptism: 'Sim', phone: '(11) 99999-0000' },
  { id: 2, name: 'Maria Santos', role: 'Membro', status: 'Ativo', baptism: 'Sim', phone: '(11) 98888-1111' },
  { id: 3, name: 'Lucas Oliveira', role: 'Líder de Célula', status: 'Ativo', baptism: 'Sim', phone: '(11) 97777-2222' },
];

const INITIAL_FINANCES = [
  { id: 1, description: 'Dízimo - João Silva', amount: 500, type: 'entrada', date: '2023-10-25' },
  { id: 2, description: 'Aluguel do Templo', amount: 2500, type: 'saida', date: '2023-10-20' },
  { id: 3, description: 'Oferta Culto de Domingo', amount: 1200, type: 'entrada', date: '2023-10-22' },
];

const App = () => {
  const [activeTab, setActiveTab] = useState('dashboard');
  const [members, setMembers] = useState(INITIAL_MEMBERS);
  const [finances, setFinances] = useState(INITIAL_FINANCES);
  const [isSidebarOpen, setIsSidebarOpen] = useState(true);

  // --- Calculations ---
  const totals = useMemo(() => {
    const income = finances.filter(f => f.type === 'entrada').reduce((acc, curr) => acc + curr.amount, 0);
    const expenses = finances.filter(f => f.type === 'saida').reduce((acc, curr) => acc + curr.amount, 0);
    return { income, expenses, balance: income - expenses };
  }, [finances]);

  // --- Components ---
  const SidebarItem = ({ id, icon: Icon, label }) => (
    <button
      onClick={() => setActiveTab(id)}
      className={`w-full flex items-center space-x-3 px-4 py-3 rounded-lg transition-colors ${
        activeTab === id ? 'bg-blue-600 text-white shadow-lg' : 'text-slate-600 hover:bg-slate-100'
      }`}
    >
      <Icon size={20} />
      <span className="font-medium">{label}</span>
    </button>
  );

  const Card = ({ title, value, icon: Icon, color, trend }) => (
    <div className="bg-white p-6 rounded-xl shadow-sm border border-slate-100">
      <div className="flex justify-between items-start mb-4">
        <div className={`p-2 rounded-lg ${color}`}>
          <Icon size={24} className="text-white" />
        </div>
        {trend && (
          <span className="text-xs font-semibold text-green-600 bg-green-50 px-2 py-1 rounded-full">
            {trend}
          </span>
        )}
      </div>
      <h3 className="text-slate-500 text-sm font-medium">{title}</h3>
      <p className="text-2xl font-bold text-slate-800 mt-1">{value}</p>
    </div>
  );

  return (
    <div className="min-h-screen bg-slate-50 flex">
      {/* Sidebar */}
      <aside className={`fixed inset-y-0 left-0 z-50 w-64 bg-white border-r transform transition-transform duration-200 ease-in-out ${isSidebarOpen ? 'translate-x-0' : '-translate-x-full'} lg:relative lg:translate-x-0`}>
        <div className="p-6">
          <div className="flex items-center space-x-3 text-blue-600 mb-8">
            <Church size={32} weight="fill" />
            <span className="text-xl font-bold tracking-tight text-slate-800">EclésiaGest</span>
          </div>
          <nav className="space-y-2">
            <SidebarItem id="dashboard" icon={LayoutDashboard} label="Painel Geral" />
            <SidebarItem id="members" icon={Users} label="Membros" />
            <SidebarItem id="finance" icon={HandCoins} label="Financeiro" />
            <SidebarItem id="cells" icon={Church} label="Células" />
            <SidebarItem id="events" icon={Calendar} label="Agenda" />
          </nav>
        </div>
        <div className="absolute bottom-0 w-full p-6 border-t">
          <div className="flex items-center space-x-3">
            <div className="w-10 h-10 rounded-full bg-slate-200 flex items-center justify-center font-bold text-slate-600">
              PR
            </div>
            <div>
              <p className="text-sm font-bold text-slate-800">Pr. Ricardo</p>
              <p className="text-xs text-slate-500">Administrador</p>
            </div>
          </div>
        </div>
      </aside>

      {/* Main Content */}
      <main className="flex-1 overflow-y-auto h-screen">
        <header className="bg-white border-b px-8 py-4 sticky top-0 z-40 flex items-center justify-between">
          <div className="flex items-center space-x-4">
            <button onClick={() => setIsSidebarOpen(!isSidebarOpen)} className="lg:hidden p-2 text-slate-600">
              <Menu />
            </button>
            <h1 className="text-xl font-bold text-slate-800 capitalize">
              {activeTab === 'dashboard' ? 'Dashboard' : activeTab}
            </h1>
          </div>
          <div className="flex items-center space-x-4">
            <div className="hidden md:flex items-center bg-slate-100 rounded-lg px-3 py-2 border">
              <Search size={18} className="text-slate-400 mr-2" />
              <input 
                type="text" 
                placeholder="Buscar membro..." 
                className="bg-transparent border-none outline-none text-sm w-64"
              />
            </div>
            <button className="bg-blue-600 text-white p-2 rounded-lg hover:bg-blue-700 transition-colors">
              <Plus size={20} />
            </button>
          </div>
        </header>

        <div className="p-8">
          {activeTab === 'dashboard' && (
            <div className="space-y-8">
              {/* Stats Grid */}
              <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
                <Card 
                  title="Total de Membros" 
                  value={members.length} 
                  icon={Users} 
                  color="bg-blue-500" 
                  trend="+2 esse mês"
                />
                <Card 
                  title="Entradas (Mês)" 
                  value={`R$ ${totals.income.toLocaleString()}`} 
                  icon={ArrowUpCircle} 
                  color="bg-green-500" 
                />
                <Card 
                  title="Saídas (Mês)" 
                  value={`R$ ${totals.expenses.toLocaleString()}`} 
                  icon={ArrowDownCircle} 
                  color="bg-red-500" 
                />
                <Card 
                  title="Saldo Atual" 
                  value={`R$ ${totals.balance.toLocaleString()}`} 
                  icon={TrendingUp} 
                  color="bg-purple-500" 
                />
              </div>

              {/* Recent Activity & Quick Actions */}
              <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
                <div className="lg:col-span-2 bg-white rounded-xl shadow-sm border border-slate-100 p-6">
                  <div className="flex justify-between items-center mb-6">
                    <h2 className="text-lg font-bold text-slate-800">Últimas Transações</h2>
                    <button className="text-blue-600 text-sm font-semibold hover:underline">Ver tudo</button>
                  </div>
                  <div className="space-y-4">
                    {finances.map((item) => (
                      <div key={item.id} className="flex items-center justify-between p-4 rounded-lg hover:bg-slate-50 transition-colors border border-transparent hover:border-slate-100">
                        <div className="flex items-center space-x-4">
                          <div className={`p-2 rounded-full ${item.type === 'entrada' ? 'bg-green-100 text-green-600' : 'bg-red-100 text-red-600'}`}>
                            {item.type === 'entrada' ? <ArrowUpCircle size={20} /> : <ArrowDownCircle size={20} />}
                          </div>
                          <div>
                            <p className="font-semibold text-slate-800">{item.description}</p>
                            <p className="text-xs text-slate-500">{new Date(item.date).toLocaleDateString('pt-BR')}</p>
                          </div>
                        </div>
                        <span className={`font-bold ${item.type === 'entrada' ? 'text-green-600' : 'text-red-600'}`}>
                          {item.type === 'entrada' ? '+' : '-'} R$ {item.amount.toLocaleString()}
                        </span>
                      </div>
                    ))}
                  </div>
                </div>

                <div className="bg-white rounded-xl shadow-sm border border-slate-100 p-6">
                  <h2 className="text-lg font-bold text-slate-800 mb-6">Ações Rápidas</h2>
                  <div className="space-y-3">
                    <button className="w-full flex items-center justify-between p-4 bg-blue-50 text-blue-700 rounded-xl hover:bg-blue-100 transition-colors font-semibold">
                      <div className="flex items-center space-x-3">
                        <UserPlus size={20} />
                        <span>Novo Membro</span>
                      </div>
                      <ChevronRight size={18} />
                    </button>
                    <button className="w-full flex items-center justify-between p-4 bg-green-50 text-green-700 rounded-xl hover:bg-green-100 transition-colors font-semibold">
                      <div className="flex items-center space-x-3">
                        <HandCoins size={20} />
                        <span>Registrar Dízimo</span>
                      </div>
                      <ChevronRight size={18} />
                    </button>
                    <button className="w-full flex items-center justify-between p-4 bg-purple-50 text-purple-700 rounded-xl hover:bg-purple-100 transition-colors font-semibold">
                      <div className="flex items-center space-x-3">
                        <Calendar size={20} />
                        <span>Novo Evento</span>
                      </div>
                      <ChevronRight size={18} />
                    </button>
                  </div>
                </div>
              </div>
            </div>
          )}

          {activeTab === 'members' && (
            <div className="bg-white rounded-xl shadow-sm border border-slate-100 overflow-hidden">
              <table className="w-full text-left">
                <thead className="bg-slate-50 border-b text-slate-600 text-sm">
                  <tr>
                    <th className="px-6 py-4 font-semibold">Nome</th>
                    <th className="px-6 py-4 font-semibold">Cargo</th>
                    <th className="px-6 py-4 font-semibold">Telefone</th>
                    <th className="px-6 py-4 font-semibold">Batizado</th>
                    <th className="px-6 py-4 font-semibold">Status</th>
                    <th className="px-6 py-4 font-semibold">Ações</th>
                  </tr>
                </thead>
                <tbody className="divide-y divide-slate-100">
                  {members.map((member) => (
                    <tr key={member.id} className="hover:bg-slate-50 transition-colors">
                      <td className="px-6 py-4">
                        <div className="flex items-center space-x-3">
                          <div className="w-8 h-8 rounded-full bg-blue-100 text-blue-600 flex items-center justify-center font-bold text-xs">
                            {member.name.substring(0,2).toUpperCase()}
                          </div>
                          <span className="font-medium text-slate-800">{member.name}</span>
                        </div>
                      </td>
                      <td className="px-6 py-4 text-slate-600">{member.role}</td>
                      <td className="px-6 py-4 text-slate-600 text-sm">{member.phone}</td>
                      <td className="px-6 py-4">
                        <span className={`px-2 py-1 rounded text-xs font-bold ${member.baptism === 'Sim' ? 'bg-blue-100 text-blue-600' : 'bg-slate-100 text-slate-500'}`}>
                          {member.baptism === 'Sim' ? 'Batizado' : 'Não'}
                        </span>
                      </td>
                      <td className="px-6 py-4">
                        <span className="flex items-center text-green-600 text-sm font-medium">
                          <span className="w-2 h-2 bg-green-500 rounded-full mr-2"></span>
                          {member.status}
                        </span>
                      </td>
                      <td className="px-6 py-4">
                        <button className="text-slate-400 hover:text-slate-600">
                          <MoreVertical size={18} />
                        </button>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
              <div className="p-4 border-t bg-slate-50 flex justify-between items-center">
                <p className="text-sm text-slate-500">Exibindo {members.length} membros</p>
                <div className="flex space-x-2">
                  <button className="px-3 py-1 border rounded bg-white text-sm hover:bg-slate-100">Anterior</button>
                  <button className="px-3 py-1 border rounded bg-white text-sm hover:bg-slate-100">Próximo</button>
                </div>
              </div>
            </div>
          )}

          {activeTab === 'finance' && (
             <div className="space-y-6">
               <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
                 <div className="bg-white p-6 rounded-xl border-l-4 border-green-500 shadow-sm">
                   <p className="text-slate-500 text-xs font-bold uppercase tracking-wider">Entradas do Dia</p>
                   <p className="text-2xl font-black text-slate-800 mt-1">R$ 1.250,00</p>
                 </div>
                 <div className="bg-white p-6 rounded-xl border-l-4 border-red-500 shadow-sm">
                   <p className="text-slate-500 text-xs font-bold uppercase tracking-wider">Saídas do Dia</p>
                   <p className="text-2xl font-black text-slate-800 mt-1">R$ 450,00</p>
                 </div>
                 <div className="bg-white p-6 rounded-xl border-l-4 border-blue-500 shadow-sm">
                   <p className="text-slate-500 text-xs font-bold uppercase tracking-wider">Previsto no Mês</p>
                   <p className="text-2xl font-black text-slate-800 mt-1">R$ 15.000,00</p>
                 </div>
               </div>

               <div className="bg-white rounded-xl shadow-sm border overflow-hidden">
                <div className="p-6 border-b flex justify-between items-center">
                  <h2 className="font-bold text-slate-800">Fluxo de Caixa</h2>
                  <div className="flex space-x-2">
                    <button className="px-4 py-2 bg-green-600 text-white rounded-lg text-sm font-bold flex items-center">
                      <Plus size={16} className="mr-1"/> Entrada
                    </button>
                    <button className="px-4 py-2 bg-red-600 text-white rounded-lg text-sm font-bold flex items-center">
                      <Plus size={16} className="mr-1"/> Saída
                    </button>
                  </div>
                </div>
                <table className="w-full text-left">
                  <thead className="bg-slate-50 border-b text-slate-600 text-sm">
                    <tr>
                      <th className="px-6 py-4 font-semibold">Descrição</th>
                      <th className="px-6 py-4 font-semibold">Categoria</th>
                      <th className="px-6 py-4 font-semibold">Data</th>
                      <th className="px-6 py-4 font-semibold text-right">Valor</th>
                    </tr>
                  </thead>
                  <tbody className="divide-y divide-slate-100">
                    {finances.map((f) => (
                      <tr key={f.id} className="hover:bg-slate-50">
                        <td className="px-6 py-4 font-medium text-slate-800">{f.description}</td>
                        <td className="px-6 py-4">
                          <span className="text-xs font-medium px-2 py-1 bg-slate-100 rounded text-slate-600">
                            {f.type === 'entrada' ? 'Dízimo/Oferta' : 'Administrativo'}
                          </span>
                        </td>
                        <td className="px-6 py-4 text-slate-500 text-sm">{new Date(f.date).toLocaleDateString('pt-BR')}</td>
                        <td className={`px-6 py-4 text-right font-bold ${f.type === 'entrada' ? 'text-green-600' : 'text-red-600'}`}>
                          R$ {f.amount.toLocaleString()}
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
               </div>
             </div>
          )}

          {activeTab === 'cells' && (
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
              {[1, 2, 3].map(i => (
                <div key={i} className="bg-white rounded-xl border border-slate-100 shadow-sm overflow-hidden hover:shadow-md transition-shadow cursor-pointer">
                  <div className="h-2 bg-blue-500"></div>
                  <div className="p-6">
                    <div className="flex justify-between items-start mb-4">
                      <div>
                        <h3 className="font-bold text-slate-800 text-lg">Célula Boas Novas {i}</h3>
                        <p className="text-sm text-slate-500">Líder: Lucas Oliveira</p>
                      </div>
                      <span className="bg-blue-50 text-blue-600 text-xs font-bold px-2 py-1 rounded">Ativa</span>
                    </div>
                    <div className="space-y-2 mb-6 text-sm text-slate-600">
                      <div className="flex items-center space-x-2">
                        <Calendar size={14} />
                        <span>Quartas-feiras, 20h</span>
                      </div>
                      <div className="flex items-center space-x-2">
                        <Users size={14} />
                        <span>12 Membros</span>
                      </div>
                    </div>
                    <button className="w-full py-2 border border-blue-100 text-blue-600 rounded-lg text-sm font-bold hover:bg-blue-50 transition-colors">
                      Gerenciar Reunião
                    </button>
                  </div>
                </div>
              ))}
            </div>
          )}

          {activeTab === 'events' && (
            <div className="bg-white p-8 rounded-xl border text-center">
              <Calendar size={48} className="mx-auto text-slate-200 mb-4" />
              <h2 className="text-xl font-bold text-slate-800">Calendário de Atividades</h2>
              <p className="text-slate-500 mt-2 max-w-sm mx-auto">Visualize e agende cultos, ensaios e eventos especiais da igreja.</p>
              <button className="mt-6 bg-blue-600 text-white px-6 py-2 rounded-lg font-bold">
                Criar Novo Evento
              </button>
            </div>
          )}
        </div>
      </main>
    </div>
  );
};

export default App;
