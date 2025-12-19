# Election_2024_Analysis
This project is an end-to-end SQL-based data analysis of the India General Elections 2024 using MySQL. The goal of this project is to analyze election results, party performance, alliances, voting patterns, and state-wise outcomes using structured queries and relational database concepts. 
The project demonstrates strong command over:

-SQL querying
-Joins across multiple tables
-Aggregations & conditional logic
-Window functions
-Real-world political data analysis
**Database & Tables Used**
Database: Election_2024
Key Tables:
constituencywise_details – Candidate-level vote details (EVM, postal, total votes)
constituencywise_results – Constituency-level winning details
partywise_results – Party-wise seats won and alliance mapping
statewise_results – State and parliamentary constituency mapping
states – State master data
**Key Analysis Performed*
Election Statistics
Total number of seats available at national and state levels
State-wise seat distribution
Total candidates, parties, and votes (EVM + Postal)

**Party & Alliance Analysis**
Seats won by NDA, I.N.D.I.A, and Other alliances
Party-wise and alliance-wise seat comparison
State-wise alliance performance
Dynamic alliance classification using CASE and UPDATE
**Vote Analysis**
Distribution of EVM vs Postal votes
Highest EVM vote-getter per constituency
Total votes per party and per state
Margin of victory insights
**Winner & Runner-up Insights**
Winning and runner-up candidates per constituency
State-specific winner and runner-up analysis
Top candidates by vote count using window functions
**SQL Concepts Used**
JOIN (INNER JOIN across multiple tables)
GROUP BY & HAVING
CASE WHEN
COUNT(DISTINCT ...)
SUM(), ORDER BY, LIMIT
Window Functions (ROW_NUMBER() OVER (PARTITION BY...))
CTEs (WITH clause)
Data cleaning & transformation
Schema updates & column additions
**Business & Analytical Insights**
Identified dominant alliances across states
Compared voting behavior through EVM vs postal votes
Highlighted constituency-level competition using margins
Enabled scalable alliance-based reporting for elections
**Tools & Technologies**
Database: MySQL
Language: SQL
Dataset: India General Elections 2024 (public election data)
IDE: MySQL Workbench
**Use Case**
This project is ideal for:
Data Analyst / Business Analyst portfolios
Political & public policy data analysis
Real-world relational database querying practice
**Future Enhancements**
Visualization using Power BI / Tableau
State-wise dashboards
Trend analysis across election years
Advanced performance optimization

Create database Election_2024;
Use election_2024;
CREATE TABLE constituencywise_details (
    S_No INT,
    Candidate VARCHAR(100),
    Party VARCHAR(100),
    EVM_Votes INT,
    Postal_Votes INT,
    Total_Votes INT,
    Percentage_of_Votes DECIMAL(5,2),
    Constituency_ID VARCHAR(30)
);
select * from constituencywise_details;

select count(*) from constituencywise_details;

set global local_infile =1;

DROP TABLE IF EXISTS constituencywise_details;
CREATE TABLE constituencywise_details (
    S_No INT,
    Candidate VARCHAR(100),
    Party VARCHAR(100),
    EVM_Votes INT,
    Postal_Votes INT,
    Total_Votes INT,
    Percentage_of_Votes DECIMAL(5,2),
    Constituency_ID VARCHAR(30)
);

Use constituencywise_results;
-- What is the total number of seats available for elections in each state
SELECT 
    s.State AS State_Name,
    COUNT(cr.Constituency_ID) AS Total_Seats_Available
FROM 
    constituencywise_results cr
JOIN 
    statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
JOIN 
    states s ON sr.State_ID = s.State_ID
GROUP BY 
    s.State
ORDER BY 
    s.State;

-- What is the total number of seats available for elections in each state

SELECT 
COUNT(distinct Parliament_Constituency) AS Total_Seats
	FROM constituencywise_results;
    
    -- Total Seats Won by NDA Allianz
    select sum(case when Party in('Bharatiya Janata Party - BJP', 
                'Telugu Desam - TDP', 
				'Janata Dal  (United) - JD(U)',
                'Shiv Sena - SHS', 
                'AJSU Party - AJSUP', 
                'Apna Dal (Soneylal) - ADAL', 
                'Asom Gana Parishad - AGP',
                'Hindustani Awam Morcha (Secular) - HAMS', 
                'Janasena Party - JnP', 
				'Janata Dal  (Secular) - JD(S)',
                'Lok Janshakti Party(Ram Vilas) - LJPRV', 
                'Nationalist Congress Party - NCP',
                'Rashtriya Lok Dal - RLD', 
                'Sikkim Krantikari Morcha - SKM') then Won else 0 end) as NDA_TotalSeats_won from partywise_results;
                
-- Seats Won by NDA Allianz Parties

select party as party_name, won as seat_won from partywise_results where Party in ('Bharatiya Janata Party - BJP', 
        'Telugu Desam - TDP', 
		'Janata Dal  (United) - JD(U)',
        'Shiv Sena - SHS', 
        'AJSU Party - AJSUP', 
        'Apna Dal (Soneylal) - ADAL', 
        'Asom Gana Parishad - AGP',
        'Hindustani Awam Morcha (Secular) - HAMS', 
        'Janasena Party - JnP', 
		'Janata Dal  (Secular) - JD(S)',
        'Lok Janshakti Party(Ram Vilas) - LJPRV', 
        'Nationalist Congress Party - NCP',
        'Rashtriya Lok Dal - RLD', 
        'Sikkim Krantikari Morcha - SKM') order by seat_won desc;
        
        -- Total Seats Won by I.N.D.I.A. Allianz
        
        select sum(case when Party in( 'Indian National Congress - INC',
                'Aam Aadmi Party - AAAP',
                'All India Trinamool Congress - AITC',
                'Bharat Adivasi Party - BHRTADVSIP',
                'Communist Party of India  (Marxist) - CPI(M)',
                'Communist Party of India  (Marxist-Leninist)  (Liberation) - CPI(ML)(L)',
                'Communist Party of India - CPI',
                'Dravida Munnetra Kazhagam - DMK',
                'Indian Union Muslim League - IUML',
                'Nat`Jammu & Kashmir National Conference - JKN',
                'Jharkhand Mukti Morcha - JMM',
                'Jammu & Kashmir National Conference - JKN',
                'Kerala Congress - KEC',
                'Marumalarchi Dravida Munnetra Kazhagam - MDMK',
                'Nationalist Congress Party Sharadchandra Pawar - NCPSP',
                'Rashtriya Janata Dal - RJD',
                'Rashtriya Loktantrik Party - RLTP',
                'Revolutionary Socialist Party - RSP',
                'Samajwadi Party - SP',
                'Shiv Sena (Uddhav Balasaheb Thackrey) - SHSUBT',
                'Viduthalai Chiruthaigal Katchi - VCK') then Won else 0 end) as Total_Seats_won_BY_INDIA from partywise_results;
                
--                Seats Won by I.N.D.I.A. Allianz Parties
select party as Party_won, won as seats from partywise_results where party in('Indian National Congress - INC',
                'Aam Aadmi Party - AAAP',
                'All India Trinamool Congress - AITC',
                'Bharat Adivasi Party - BHRTADVSIP',
                'Communist Party of India  (Marxist) - CPI(M)',
                'Communist Party of India  (Marxist-Leninist)  (Liberation) - CPI(ML)(L)',
                'Communist Party of India - CPI',
                'Dravida Munnetra Kazhagam - DMK',
                'Indian Union Muslim League - IUML',
                'Nat`Jammu & Kashmir National Conference - JKN',
                'Jharkhand Mukti Morcha - JMM',
                'Jammu & Kashmir National Conference - JKN',
                'Kerala Congress - KEC',
                'Marumalarchi Dravida Munnetra Kazhagam - MDMK',
                'Nationalist Congress Party Sharadchandra Pawar - NCPSP',
                'Rashtriya Janata Dal - RJD',
                'Rashtriya Loktantrik Party - RLTP',
                'Revolutionary Socialist Party - RSP',
                'Samajwadi Party - SP',
                'Shiv Sena (Uddhav Balasaheb Thackrey) - SHSUBT',
                'Viduthalai Chiruthaigal Katchi - VCK') order by seats desc;
                
                -- Add new column field in table partywise_results to get the Party Allianz as NDA, I.N.D.I.A and OTHER
                
                alter table partywise_results add party_allianz varchar (50);
  SET SQL_SAFE_UPDATES = 0;
  
                update partywise_results set party_allianz = 'I.N.D.I.A' where party in('Indian National Congress - INC',
                'Aam Aadmi Party - AAAP',
                'All India Trinamool Congress - AITC',
                'Bharat Adivasi Party - BHRTADVSIP',
                'Communist Party of India  (Marxist) - CPI(M)',
                'Communist Party of India  (Marxist-Leninist)  (Liberation) - CPI(ML)(L)',
                'Communist Party of India - CPI',
                'Dravida Munnetra Kazhagam - DMK',
                'Indian Union Muslim League - IUML',
                'Nat`Jammu & Kashmir National Conference - JKN',
                'Jharkhand Mukti Morcha - JMM',
                'Jammu & Kashmir National Conference - JKN',
                'Kerala Congress - KEC',
                'Marumalarchi Dravida Munnetra Kazhagam - MDMK',
                'Nationalist Congress Party Sharadchandra Pawar - NCPSP',
                'Rashtriya Janata Dal - RJD',
                'Rashtriya Loktantrik Party - RLTP',
                'Revolutionary Socialist Party - RSP',
                'Samajwadi Party - SP',
                'Shiv Sena (Uddhav Balasaheb Thackrey) - SHSUBT',
                'Viduthalai Chiruthaigal Katchi - VCK'
    
);
select * from partywise_results;

                update partywise_results set party_allianz = 'NDA' where party in (
    'Bharatiya Janata Party - BJP',
    'Telugu Desam - TDP',
    'Janata Dal  (United) - JD(U)',
    'Shiv Sena - SHS',
    'AJSU Party - AJSUP',
    'Apna Dal (Soneylal) - ADAL',
    'Asom Gana Parishad - AGP',
    'Hindustani Awam Morcha (Secular) - HAMS',
    'Janasena Party - JnP',
    'Janata Dal  (Secular) - JD(S)',
    'Lok Janshakti Party(Ram Vilas) - LJPRV',
    'Nationalist Congress Party - NCP',
    'Rashtriya Lok Dal - RLD',
    'Sikkim Krantikari Morcha - SKM');
    
                    update partywise_results set party_allianz = 'Other' where party_allianz is null;
                    
-- Which party alliance (NDA, I.N.D.I.A, or OTHER) won the most seats across all states?

select party_allianz,sum(won) from partywise_results group by party_allianz;

-- Winning candidate's name, their party name, total votes, and the margin of victory for a specific state and constituency?

select cr.winning_candidate,pr.party,cr.total_votes,cr.margin,s.state,cr.constituency_name
from constituencywise_results cr
join partywise_results pr on cr.party_id = pr.party_id
join statewise_results sr on cr.parliament_constituency = sr.parliament_constituency
join states s on sr.state_id = s.state_id 
where cr.constituency_name ='AGRA';

-- What is the distribution of EVM votes versus postal votes for candidates in a specific constituency?

select cr.constituency_name,cd.candidate,cd.postal_votes,cd.evm_votes,cd.total_votes 
from constituencywise_results cr
join constituencywise_details cd on cr.constituency_id = cd.constituency_id
where cr.constituency_name='MATHURA';

-- Which parties won the most seats in each State, and how many votes did each party get?

SELECT sr.state as state, cd.party as party ,SUM(cr.total_votes) as total_votes from constituencywise_results cr 
join statewise_results sr 
on cr.parliament_constituency = sr.parliament_constituency
join constituencywise_details cd on cr.constituency_id=cd.constituency_id
join states s on sr.state_id=s.state_id
where s.state= 'Himachal Pradesh'
group by sr.state, cd.party
order by sr.state, total_votes desc
;

-- What is the total number of seats won by each party alliance (NDA, I.N.D.I.A, and OTHER) in each state for the India Elections 2024

select s.state as STATE_NAME, sum(case when pr.party_allianz ='NDA' then 1 else 0 end) as NDA_seats_won,
sum(case when pr.party_allianz ='I.N.D.I.A' then 1 else 0 end) as INDIA_seats_won,
sum(case when pr.party_allianz ='Other' then 1 else 0 end) as Other_seats_won
from constituencywise_results cr 
join statewise_results sr on cr.parliament_constituency=sr.parliament_constituency
join constituencywise_details cd on cr.constituency_id=cd.constituency_id
join states s on sr.state_id=s.state_id
join partywise_results pr on pr.party_id=cr.party_id
where pr.party_allianz in('NDA','I.N.D.I.A','Other')
group by s.state
order by  s.state;

-- Which candidate received the highest number of EVM votes in each constituency (Top 10)?

WITH ranked_candidates AS (
    SELECT 
        cr.constituency_name,
        cd.constituency_id,
        cd.candidate,
        cd.EVM_votes,
        ROW_NUMBER() OVER (
            PARTITION BY cd.constituency_id 
            ORDER BY cd.EVM_votes DESC
        ) AS rn
    FROM 
        constituencywise_details cd
    JOIN 
        constituencywise_results cr ON cd.constituency_id = cr.constituency_id
)
SELECT 
    constituency_name,
    constituency_id,
    candidate,
    EVM_votes
FROM 
    ranked_candidates
WHERE 
    rn = 1
ORDER BY 
    EVM_votes DESC
LIMIT 10;

-- Which candidate won and which candidate was the runner-up in each constituency of State for the 2024 elections?

SELECT 
    cr.Constituency_Name,
    
    (SELECT cd1.Candidate
     FROM constituencywise_details cd1
     WHERE cd1.Constituency_ID = cr.Constituency_ID
     ORDER BY (cd1.EVM_Votes + cd1.Postal_Votes) DESC
     LIMIT 1) AS Winning_Candidate,
     
    (SELECT cd2.Candidate
     FROM constituencywise_details cd2
     WHERE cd2.Constituency_ID = cr.Constituency_ID
     ORDER BY (cd2.EVM_Votes + cd2.Postal_Votes) DESC
     LIMIT 1 OFFSET 1) AS Runnerup_Candidate

FROM 
    constituencywise_results cr
JOIN 
    statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
JOIN 
    states s ON sr.State_ID = s.State_ID
WHERE 
    s.State = 'Maharashtra'
GROUP BY 
    cr.Constituency_Name, cr.Constituency_ID
ORDER BY 
    cr.Constituency_Name;

-- For the state of Maharashtra, what are the total number of seats, 
-- total number of candidates, total number of parties, total votes (including EVM and postal),
-- and the breakdown of EVM and postal votes?

SELECT 
    COUNT(DISTINCT cr.Constituency_ID) AS Total_Seats,
    COUNT(DISTINCT cd.Candidate) AS Total_Candidates,
    COUNT(DISTINCT p.Party) AS Total_Parties,
    SUM(cd.EVM_Votes + cd.Postal_Votes) AS Total_Votes,
    SUM(cd.EVM_Votes) AS Total_EVM_Votes,
    SUM(cd.Postal_Votes) AS Total_Postal_Votes
FROM 
    constituencywise_results cr
JOIN 
    constituencywise_details cd ON cr.Constituency_ID = cd.Constituency_ID
JOIN 
    statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
JOIN 
    states s ON sr.State_ID = s.State_ID
JOIN 
    partywise_results p ON cr.Party_ID = p.Party_ID
WHERE 
    s.State = 'Maharashtra';


    ---END OF PROJECT---
