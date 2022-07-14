select * 
From [Portfolio Project]. . ['Covid Deaths$']
where continent is not null 
order by 3, 4

--select *
--From	[Portfolio Project]. . ['Covid Vax$']
--order by 3, 4

--Select Data that we are going to use 

select location, date, total_cases, new_cases, total_deaths, population
From [Portfolio Project]. . ['Covid Deaths$']
where continent is not null 
order by 1,2

-- Looking at Total cases vs Total Deaths 

select location, date, total_cases, total_deaths, (total_deaths/total_cases) * 100 as Death_Percentage
From [Portfolio Project]. . ['Covid Deaths$']
where location like '%canada%'
order by 1,2

-- Looking at total cases vs population 
-- % of population got covid 

select location, date, population, total_cases, (total_cases/population) * 100 as CovidPopulation_Percentage
From [Portfolio Project]. . ['Covid Deaths$']
--where location like '%canada%'
where continent is not null 
order by 1,2

--looking at countries with highest infection rate compared to population


select location, population, Max(total_cases) as Highest_Infection_Count, Max((total_cases/population)) * 100 as Percet_Population_Infected
From [Portfolio Project]. . ['Covid Deaths$']
--where location like '%canada%' 
where continent is not null 
group by location, population
order by Percet_Population_Infected desc


--Showing countries with the Highest Death Count Per Population

select location, Max(cast(total_deaths as int)) as Total_Death_Count
From [Portfolio Project]. . ['Covid Deaths$']
--where location like '%canada%' 
where continent is not null 
group by location
order by Total_Death_Count desc

--Let`s break things down by continent 


--showing continents with the ighest death count per population 

select continent, Max(cast(total_deaths as int)) as Total_Death_Count
From [Portfolio Project]. . ['Covid Deaths$']
--where location like '%canada%' 
where continent is not null 
group by continent
order by Total_Death_Count desc

--Global numbers 

select Sum(new_cases) as total_cases, sum(cast(new_deaths as int)) as total_deaths, sum(cast(new_deaths as int))/Sum(new_cases) * 100 as Death_Percentage
From [Portfolio Project]. . ['Covid Deaths$']
--where location like '%canada%'
where continent is not null
--group by date
order by 1,2

-- looking at total Population vs Vaccinations 

select dea.continent, dea.location, dea.date, dea.population, vax.new_vaccinations
, SUM(cast(vax.new_vaccinations as bigint)) OVER (Partition by dea.location ORDER by dea.location, dea.date) as Rolling_People_Vax
From [Portfolio Project]. . ['Covid Deaths$'] dea
Join [Portfolio Project]. . ['Covid Vax$'] vax
	ON dea.location = vax.location
	and dea.date = vax.date
where dea.continent is not null 
order by 2,3

-- Using CTE 

With PopvsVax (Continent, Location, Date, Population, New_Vaccinations, Rolling_People_Vax)
as
(
select dea.continent, dea.location, dea.date, dea.population, vax.new_vaccinations
, SUM(cast(vax.new_vaccinations as bigint)) OVER (Partition by dea.location ORDER by dea.location, dea.date) as Rolling_People_Vax
From [Portfolio Project]. . ['Covid Deaths$'] dea
Join [Portfolio Project]. . ['Covid Vax$'] vax
	ON dea.location = vax.location
	and dea.date = vax.date
where dea.continent is not null 
--order by 2,3
)
Select *, (Rolling_People_Vax/Population) * 100
From PopvsVax

-- Using Temp Table to perform Calculations on Partition By in previous query 

Drop Table if exists #Percent_Population_Vaccinated
Create Table #Percent_Population_Vaccinated
(
Continent nvarchar(255),
Location nvarchar(255),
Date datetime,
Population numeric,
New_Vaccinations numeric, 
Rolling_People_Vax numeric
)

insert into #Percent_Population_Vaccinated
select dea.continent, dea.location, dea.date, dea.population, vax.new_vaccinations
, SUM(cast(vax.new_vaccinations as bigint)) OVER (Partition by dea.location ORDER by dea.location, dea.date) as Rolling_People_Vax
From [Portfolio Project]. . ['Covid Deaths$'] dea
Join [Portfolio Project]. . ['Covid Vax$'] vax
	ON dea.location = vax.location
	and dea.date = vax.date
--where dea.continent is not null 
--order by 2,3

Select *, (Rolling_People_Vax/Population) * 100
From #Percent_Population_Vaccinated


-- Creating View to store data for later visualizations 

GO
create view Percent_Population_Vaccinated as 
select dea.continent, dea.location, dea.date, dea.population, vax.new_vaccinations
, SUM(cast(vax.new_vaccinations as bigint)) OVER (Partition by dea.location ORDER by dea.location, dea.date) as Rolling_People_Vax
From [Portfolio Project]. . ['Covid Deaths$'] dea
Join [Portfolio Project]. . ['Covid Vax$'] vax
	ON dea.location = vax.location
	and dea.date = vax.date
where dea.continent is not null 
