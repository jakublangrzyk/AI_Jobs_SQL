Projekt SQL: Analiza ofert pracy w branży AI

Dane
Dane pochodzą z Kaggle (AI Job Market Dataset).  
Baza danych została podzielona na 4 tabele:  
- jobs – informacje o ofertach pracy (stanowisko, pensja, doświadczenie itp.)  
- companies – dane o firmach (nazwa, lokalizacja, wielkość)  
- skills – lista umiejętności (np. Python, SQL, Machine Learning)  
- job_skills – tabela łącząca oferty pracy z wymaganymi umiejętnościami  

Cele analizy
1. TOP 10 najczęściej wymaganych umiejętności  
2. Średnia pensja w zależności od poziomu doświadczenia  
3. Firmy z największą liczbą ofert  
4. Typ pracy a średnia pensja
5. Jakie umiejętności występują w ofertach powyżej średniej pensji  

Do każdego zadania załączam zrzut ekranu rozwiązania w plikach


1. TOP 10 najczęściej wymaganych umiejętności
```sql
select count(*) as liczba_ofert, s.skill_name as umiejetnosc 
from job_skills js 
left join skills s on js.skill_id = s.skill_id
group by s.skill_name
order by liczba_ofert desc
limit 10;
```
Python i Sql to wiodące technologie wymagane w ofertach pracy.

2. Średnia pensja w zależności od poziomu doświadczenia  
```sql
select experience_level,
round(avg(salary_usd),2) as srednia_pensja
from jobs
group by experience_level
order by srednia_pensja desc;
```
Wyniki analizy dokładnie pokazują, że wraz ze wzrostem poziomu doświadczenia rosną również zarobki.

3. Firmy z największą liczbą ofert  
```sql
select count(*) as liczba_ofert, c.company_name
from jobs j
left join companies c on j.company_id = c.company_id
group by c.company_name
order by liczba_ofert desc;
```
Firma z największą liczbą ofert w wysokości 980 to TechCorp Inc. Analiza przedstawia ranking kolejnych firm, posortowanych od największej liczby ofert do najmniejszej.

4. Typ pracy a średnia pensja
```sql
select 
case when remote_ratio = 100 then 'Zdalna'
when remote_ratio = 50 then 'Hybrydowa'
else 'Stacjonarna' end as typ_pracy,
count(*) as liczba_ofert,
round(avg(salary_usd),0) as srednia_pensja
from jobs
group by typ_pracy
order by srednia_pensja desc;
```
Średnie pensje w poszczególnych typach pracy są bardzo zbliżone do siebie, na najwyższe pensje mogą liczyć osoby pracujące zdalnie, choć różnica względem stacjonarnych czy hybrydowych jest niewielka ok. 2 tys. USD.

5. Jakie umiejętności występują w ofertach powyżej średniej pensji  
```sql
select js.job_id, j.job_title, s.skill_name, round(avg(salary_usd),2) as srednie_zarobki
from job_skills js
left join jobs j on js.job_id = j.job_id
left join skills s on js.skill_id = s.skill_id
where j.salary_usd > (
select avg(j1.salary_usd)
from jobs j1 )
group by  js.job_id, j.job_title, s.skill_name
order by srednie_zarobki desc;
```
Analiza pokazuje, że w ofertach z wynagrodzeniami powyżej średniej często pojawiają się takie umiejętności jak Python, SQL czy Machine Learning. Pensja została przypisana do całej oferty, a nie do pojedynczej umiejętności
