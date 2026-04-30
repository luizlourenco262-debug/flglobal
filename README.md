# flglobal

```tsx
import Link from "next/link";
import { supabase } from "@/lib/supabase";
import { useRouter } from "next/router";
import { useEffect, useState } from "react";
import useTheme from "@/hooks/useTheme";

export default function Navbar(){
  const router = useRouter();
  const [user, setUser] = useState(null);
  const [menuAberto, setMenuAberto] = useState(false);
  const { theme, toggleTheme } = useTheme();

  useEffect(()=>{
    supabase.auth.getSession().then(({ data })=>{
      setUser(data.session?.user || null);
    });
  },[]);

  async function handleLogout(){
    await supabase.auth.signOut();
    router.push("/login");
  }

  return (
    <nav className="bg-slate-800 text-white p-4 flex justify-between items-center">
      <div className="text-xl font-bold">FL Global</div>

      {/* Menu desktop */}
      <div className="hidden md:flex gap-6">
        <Link href="/">Loja</Link>
        <Link href="/dashboard">Dashboard</Link>
        <Link href="/player">Player</Link>
        <Link href="/painel">Painel Externo</Link>
      </div>

      {/* Botões */}
      <div className="hidden md:flex gap-4 items-center">
        <button 
          onClick={toggleTheme} 
          className="bg-yellow-500 px-3 py-1 rounded"
        >
          {theme === "dark" ? "☀️ Claro" : "🌙 Escuro"}
        </button>
        {user ? (
          <button onClick={handleLogout} className="bg-red-500 px-3 py-1 rounded">
            Logout
          </button>
        ) : (
          <>
            <Link href="/login">Login</Link>
            <Link href="/signup">Cadastro</Link>
          </>
        )}
      </div>

      {/* Hambúrguer mobile */}
      <button className="md:hidden" onClick={()=>setMenuAberto(!menuAberto)}>☰</button>

      {/* Menu mobile */}
      {menuAberto && (
        <div className="absolute top-16 left-0 w-full bg-slate-700 p-4 flex flex-col gap-4 md:hidden">
          <Link href="/">Loja</Link>
          <Link href="/dashboard">Dashboard</Link>
          <Link href="/player">Player</Link>
          <Link href="/painel">Painel Externo</Link>
          <button onClick={toggleTheme} className="bg-yellow-500 px-3 py-1 rounded">
            {theme === "dark" ? "☀️ Claro" : "🌙 Escuro"}
          </button>
          {user ? (
            <button onClick={handleLogout} className="bg-red-500 px-3 py-1 rounded">Logout</button>
          ) : (
            <>
              <Link href="/login">Login</Link>
              <Link href="/signup">Cadastro</Link>
            </>
          )}
        </div>
      )}
    </nav>
  );
}