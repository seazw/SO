# SO
Obiectivele lucrării 

Însușirea modalităților de creare a mecanismelor de planificare; 

Însușirea modalităților de prelucrare a mecanismului de planificare; 

Utilizarea platformei GitHub pentru lucrul în grup asupra codului sursă. 

1. Descrierea aplicației realizate 

Aplicația implementează sarcina lucrării folosind clasele java.util.Timer și java.util.TimerTask. Programul planifică trei timere independente, fiecare demonstrând un mod diferit de utilizare a metodelor de planificare, conform cerințelor: 

Reacție la un anumit interval de timp — implementată cu metoda schedule(TimerTask, long delay): timerul "TimerInterval" execută o singură dată, la 3 secunde de la pornirea aplicației. 

Reacție la un anumit moment de timp — implementată cu metoda schedule(TimerTask, Date time): timerul "TimerLaOra" calculează, folosind clasa Calendar, un moment exact (ora curentă + 8 secunde) și execută o singură dată la acel moment. 

Reacție cu o perioadă indicată — implementată cu metoda scheduleAtFixedRate(TimerTask, long delay, long period): timerul "TimerPeriodic" se repetă la fiecare 1 secundă, de 5 ori, apoi se oprește singur apelând this.cancel() din interiorul metodei run(). 

Suplimentar, un al patrulea timer ("TimerOprireAplicatie") oprește forțat toate cele trei timere și închide aplicația (System.exit(0)) după 10 secunde, timp suficient pentru ca toate execuțiile programate să se fi produs. 

2. Codul sursă (TimerDemo.java) 

import java.awt.Toolkit; 

import java.util.Calendar; 

import java.util.Date; 

import java.util.Timer; 

import java.util.TimerTask; 

  

/** 

 * Lucrare de laborator 1 

 * Tema: Elaborarea unui mecanism de planificare a activitatii proceselor 

 *       utilizand Timer-ul. 

 * 

 * Aplicatia demonstreaza cele 3 moduri de planificare cerute in sarcina 

 * lucrarii, folosind clasele Timer si TimerTask: 

 * 

 *   1. TimerInterval  -> reactioneaza dupa un anumit INTERVAL de timp (delay) 

 *   2. TimerLaOra     -> reactioneaza la un anumit MOMENT DE TIMP exact 

 *   3. TimerPeriodic  -> reactioneaza repetat, cu o PERIOADA indicata 

 */ 

public class TimerDemo { 

  

    public static void main(String[] args) { 

  

        // ------------------------------------------------------------ 

        // 1) Timer care reactioneaza dupa un anumit INTERVAL de timp 

        //    (schedule(task, delay) -> o singura executie, dupa "delay" ms) 

        // ------------------------------------------------------------ 

        final long intervalMs = 3000; // reactioneaza dupa 3 secunde 

        Timer timerInterval = new Timer("TimerInterval"); 

        timerInterval.schedule(new TimerTask() { 

            @Override 

            public void run() { 

                System.out.println("[Interval] Au trecut " + (intervalMs / 1000) 

                        + " secunde de la pornirea aplicatiei."); 

                beepSafe(); 

            } 

        }, intervalMs); 

  

        // ------------------------------------------------------------ 

        // 2) Timer care reactioneaza la un ANUMIT MOMENT DE TIMP exact 

        //    (schedule(task, Date) -> executie la o data/ora precisa) 

        // ------------------------------------------------------------ 

        Calendar calendar = Calendar.getInstance(); 

        calendar.add(Calendar.SECOND, 8); // peste 8 secunde fata de acum 

        final Date momentExecutie = calendar.getTime(); 

  

        Timer timerLaOra = new Timer("TimerLaOra"); 

        timerLaOra.schedule(new TimerTask() { 

            @Override 

            public void run() { 

                System.out.println("[La ora fixa] Executie declansata la momentul: " 

                        + momentExecutie); 

            } 

        }, momentExecutie); 

  

        // ------------------------------------------------------------ 

        // 3) Timer care reactioneaza cu o PERIOADA indicata 

        //    (scheduleAtFixedRate -> executii repetate la interval fix) 

        // ------------------------------------------------------------ 

        final long delayInitial = 0; 

        final long perioada = 1000; // se repeta la fiecare 1 secunda 

  

        Timer timerPeriodic = new Timer("TimerPeriodic"); 

        timerPeriodic.scheduleAtFixedRate(new TimerTask() { 

            int contor = 0; 

  

            @Override 

            public void run() { 

                contor++; 

                System.out.println("[Periodic] Executia numarul " + contor); 

  

                if (contor >= 5) { 

                    System.out.println("[Periodic] S-au efectuat 5 executii, timer-ul se opreste."); 

                    this.cancel(); 

                } 

            } 

        }, delayInitial, perioada); 

  

        System.out.println("Aplicatia a pornit. Cele 3 timere au fost planificate..."); 

  

        // ------------------------------------------------------------ 

        // Timer auxiliar: opreste aplicatia dupa ce toate timerele 

        // de mai sus si-au terminat treaba (10 secunde sunt suficiente) 

        // ------------------------------------------------------------ 

        new Timer("TimerOprireAplicatie").schedule(new TimerTask() { 

            @Override 

            public void run() { 

                System.out.println("Toate timerele si-au finalizat executia. Aplicatia se opreste."); 

                timerInterval.cancel(); 

                timerLaOra.cancel(); 

                timerPeriodic.cancel(); 

                System.exit(0); 

            } 

        }, 10000); 

    } 

  

    /** Beep "sigur" - nu arunca exceptie daca mediul e headless (fara GUI). */ 

    private static void beepSafe() { 

        try { 

            Toolkit.getDefaultToolkit().beep(); 

        } catch (Exception e) { 

            // ignoram - unele medii (server, CI) nu au suport audio/GUI 

        } 

    } 

} 

3. Rezultatul rulării 

La execuția programului (javac TimerDemo.java && java TimerDemo), în consolă se obține, aproximativ, următoarea secvență de mesaje (ordinea exactă a liniilor [Periodic] și [Interval] poate varia cu câteva milisecunde, în funcție de planificatorul de fire de execuție al JVM-ului): 

Aplicatia a pornit. Cele 3 timere au fost planificate... 

[Periodic] Executia numarul 1 

[Periodic] Executia numarul 2 

[Periodic] Executia numarul 3 

[Interval] Au trecut 3 secunde de la pornirea aplicatiei. 

[Periodic] Executia numarul 4 

[Periodic] Executia numarul 5 

[Periodic] S-au efectuat 5 executii, timer-ul se opreste. 

[La ora fixa] Executie declansata la momentul: Tue Sep 22 14:03:18 EEST 2026 

Toate timerele si-au finalizat executia. Aplicatia se opreste. 

4. Utilizarea mediului Git în grup 

Pentru lucrul în echipă asupra codului sursă a fost folosită platforma GitHub, urmând etapele de realizare indicate în lucrare: 

Crearea unui cont pe GitHub (https://github.com/) și comunicarea numelui de utilizator profesorului. 

Instalarea mediului Git local și pregătirea folderului de lucru al aplicației. 

Inițializarea depozitului local cu git init. 

Implementarea aplicației TimerDemo.java, cu împărțirea sarcinilor în echipă (fiecare membru a implementat câte un tip de timer). 

Adăugarea și înregistrarea modificărilor în depozitul local (git add, git commit). 

Crearea unei ramuri (branch) separate pentru fiecare membru al echipei și trimiterea modificărilor pe GitHub (git push), urmată de merge în ramura principală. 

Comenzile Git utilizate în cadrul lucrării: 

git init 

git add TimerDemo.java 

git commit -m "Implementare Timer/TimerTask - lucrare laborator 1" 

git branch -M main 

git remote add origin https://github.com/<utilizator>/<nume-repo>.git 

git push -u origin main 

Link către codul sursă de pe GitHub: 

https://github.com/seazw/SO/blob/main/lab1 

5. Răspunsuri la întrebările de verificare 

1. Ce este GIT? 

Git este un sistem de control al versiunilor distribuit, gratuit și open-source, conceput pentru a lucra rapid și eficient cu proiecte de orice dimensiune. Spre deosebire de sistemele centralizate (SVN, CVS), fiecare utilizator deține o copie completă a istoricului proiectului, ceea ce permite ramificare (branching) și combinare (merging) rapidă, lucru offline și lipsa unui singur punct de eșec. 

2. Componentele de bază în GIT? 

Depozitul local (repository) — istoricul complet al proiectului aflat pe calculatorul utilizatorului; zona de lucru (working directory) — fișierele efective editate; zona de așteptare (staging area / index) — modificările pregătite pentru commit; commit-urile — instantanee salvate ale proiectului; ramurile (branches) — linii independente de dezvoltare; și depozitul la distanță (remote / origin), de exemplu un repository găzduit pe GitHub. 

3. Funcțiile de bază ale lui GIT? 

git init (inițializează un depozit), git clone (copiază un depozit existent), git add (adaugă modificări în staging area), git commit (salvează un instantaneu al modificărilor), git status și git log (afișează starea, respectiv istoricul), git branch și git merge (creează, respectiv combină ramuri), git push și git pull (trimite, respectiv preia modificări de pe/pe depozitul la distanță). 

4. Dați definiția unui Timer. 

Timer-ul este un dispozitiv/obiect care notifică periodic aplicația că a expirat o anumită perioadă de timp prestabilită. Programul specifică timerului intervalul de timp (de expirare), iar acesta declanșează, la momentul potrivit, execuția unei acțiuni planificate — fiind esențial pentru aplicațiile care necesită planificarea proceselor în timp. 

5. Pentru ce este folosită metoda schedule() și din ce clasă Java ea vine? 

Metoda schedule() este folosită pentru a planifica execuția unui obiect TimerTask, fie o singură dată (după un anumit interval de întârziere sau la un moment exact dat prin obiect Date), fie repetat la o perioadă indicată. Metoda provine din clasa java.util.Timer și este supraîncărcată în mai multe variante: schedule(TimerTask, long delay), schedule(TimerTask, Date time), schedule(TimerTask, long delay, long period) și schedule(TimerTask, Date time, long period). 

6. Enumerați pașii care trebuie urmați pentru crearea unui timer. 

(1) Crearea unei subclase a clasei TimerTask și suprascrierea metodei run() cu acțiunea planificată (sau folosirea unei clase anonime); (2) crearea unui fir de execuție prin instanțierea clasei Timer; (3) crearea unui obiect de tip acțiune (instanța TimerTask); (4) planificarea execuției obiectului de tip acțiune folosind metoda schedule() sau scheduleAtFixedRate() din clasa Timer. 

7. Care este diferența dintre metoda schedule() și scheduleAtFixedRate()? 

schedule() realizează o planificare cu întârziere fixă (fixed-delay): dacă o execuție este întârziată dintr-un motiv oarecare, toate execuțiile ulterioare vor fi întârziate în consecință, decalajul acumulându-se. scheduleAtFixedRate() realizează o planificare cu rată fixă (fixed-rate): dacă o execuție este întârziată, execuțiile următoare pot avea loc mai des ("recuperând" timpul pierdut), astfel încât numărul total de execuții într-o perioadă dată rămâne constant. 

8. Când se oprește executarea unui timer? 

Un timer se oprește automat atunci când toate task-urile planificate (fără repetare) și-au terminat execuția metodei run(). El poate fi oprit și forțat, înainte de finalizarea naturală, prin apelarea metodei cancel() — după care nu mai poate fi folosit pentru planificarea altor acțiuni. De asemenea, apelul System.exit() oprește forțat toate firele de execuție, inclusiv timerele, și termină aplicația curentă. 

9. Ce metode se folosesc pentru oprirea forțată a unui timer? 

Metoda cancel() a clasei Timer oprește imediat toate task-urile planificate ale acelui timer și nu mai permite planificarea altora noi; metoda cancel() a clasei TimerTask oprește doar acel task individual (util în interiorul metodei run(), ca în cazul timerului periodic din aplicația de mai sus). Pentru oprirea întregii aplicații, inclusiv a tuturor firelor de execuție, se folosește System.exit(). 

Concluzie 

În cadrul acestei lucrări de laborator au fost însușite modalitățile de creare și prelucrare a mecanismelor de planificare a proceselor în Java, utilizând clasele Timer și TimerTask. A fost implementată o aplicație care demonstrează cele trei moduri de planificare cerute — după un interval, la un moment exact și cu o perioadă fixă — iar codul sursă a fost gestionat folosind sistemul de control al versiunilor Git și platforma GitHub, ceea ce a permis exersarea unui flux de lucru colaborativ tipic pentru dezvoltarea de software în echipă. 
