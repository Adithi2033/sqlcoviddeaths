--select * from PortfolioProject.dbo.CovidDeaths
select location,date,total_cases,total_deaths,(total_deaths*1.0/total_cases)*100 as DeathPercentage 
from PortfolioProject.dbo.CovidDeaths 
order by 1,2

--Shows Percentage of people who are affected by Covid are dead in United States
select location,date,total_cases,total_deaths,(total_deaths*1.0/total_cases)*100 as DeathPercentage 
from PortfolioProject.dbo.CovidDeaths 
where location like  '%states%'
--and continent is not null
order by 1,2 DESC

--Shows Percentage ofPopulation that are affected by Covid in United States
select location,date,population,total_cases,(total_cases*1.0/Population)*100 as CasesPercentage 
from PortfolioProject.dbo.CovidDeaths 
where location like  '%states%'
order by 1,2 DESC

--shows highly Infected cases based on location and population
select location,population,max(total_cases) as HighlyInfected,max((total_cases*1.0/population))*100 as CasesPercentage
from PortfolioProject.dbo.CovidDeaths
group by location,population
order by CasesPercentage DESC

-- shows the maximum death counts in all the countries
select location,CONTINENT,max(total_deaths) as MaxDeathsCount
from PortfolioProject.dbo.CovidDeaths
where CONTINENT IS NOT  NULL
group by location,CONTINENT
order by MaxDeathsCount DESC

--- Breaking things down by continent is null
select location,max(total_deaths) as MaxDeathsCount
from PortfolioProject.dbo.CovidDeaths
where CONTINENT IS NULL
group by location
order by MaxDeathsCount DESC

select CONTINENT,max(total_deaths) as MaxDeathsCount
from PortfolioProject.dbo.CovidDeaths
where CONTINENT IS NOT  NULL
group by CONTINENT
order by MaxDeathsCount DESC

--Overall i.e. total new_death death percentage across the globe for each day 
select date,sum(new_cases) as totalNewCases ,sum(new_deaths) as totalNewDeaths, sum(new_deaths*1.0)/sum(NULLIF(new_cases,0))*100 as NewDeathPercentage
from PortfolioProject.dbo.CovidDeaths
group by date
order by date desc  

--Over all total new death percentage acroos the globe 
select sum(new_cases) as totalNewCases ,sum(new_deaths) as totalNewDeaths, sum(new_deaths*1.0)/sum(NULLIF(new_cases,0))*100 as NewDeathPercentage
from PortfolioProject.dbo.CovidDeaths
where continent is not null
order by 1,2 DESC

--shows the total vaccinations partitioned by location and ordered by location and date i.e.Rolling vaccination count 
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CAST(vac.new_vaccinations as BIGINT)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 
order by 2,3

--Use CTE to find total Rolling count vaccination percentage from total population
With PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated)
as
(
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CAST(vac.new_vaccinations as BIGINT)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated

From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 
)
Select *, (RollingPeopleVaccinated*1.0/Population)*100
From PopvsVac
order by 2,3

-- Using Temp Table to perform Calculation on Partition By in previous query

DROP Table if exists #PercentPopulationVaccinated
Create Table #PercentPopulationVaccinated
(
Continent nvarchar(255),
Location nvarchar(255),
Date datetime,
Population numeric,
New_vaccinations numeric,
RollingPeopleVaccinated numeric
)
Insert into #PercentPopulationVaccinated
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CAST(vac.new_vaccinations as BIGINT)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date


Select *, (RollingPeopleVaccinated*1.0/Population)*100
From #PercentPopulationVaccinated

-- Creating View to store data for later visualizations

Create View PercentPopulationVaccinated as
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(cast(vac.new_vaccinations as BIGINT)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 

select * from dbo.PercentPopulationVaccinated