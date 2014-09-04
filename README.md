Resume Scoring
============

The resume scoring app will evolve into two projects.

1. A new applicant resume scoring component to aide in silencing the noise involved in screening new applicants
2. A C# api wrapper for Resumator to be published in NUGET and open sourced for all to use

## 1 - New applicant resume scoring component

The first most important feature for Clear Measure is the ability to have a configurable way to score resumes based on weighting various keywords in a resume.  Some words might be required.  Some words may boost the score of a resume.  Some words may decrease a resumes score.  Resumes that score over a set thresh-hold are left in the queue of new hires.  Resumes that don't fare so well might be toggled to a review status.  Resumes that utterly fail might be automatically flagged as failing the screening.

The scoring component should call the API a couple of times a day and iterate through all "new" status candidates.  For candidates with resumes, the resume should be parsed and the candidate status should be toggled to the next most appropriate status (not left in new).  A comment should be added to the candidate with the resume scoring results.

The scoring component will need to learn over time (and by learn I mean updated manually by the staff managing it).  This will be achieved by defining a collection of word groupings with rules pertaining to each group.  

- Found words
	- if this word appears, bump up the score by X
	- if this word appears, decrement the score by X
- Missing words
	- if this word isn't present, bump up the score by X
	- if this word isn't present, decrement the score by X

### Configuration conceptually
This app should have the ability to scan a resume for several sets of words.  Each set can have different weights and rules on whether to apply the weight on words that are found vs. words that are not found.

    <resumeScoring runFrequency="4">
	    <wordGroups>
	    	<wordGroup name="Web Programming" type="found" weight="5" words="ASP.NET, ASP.NET MVC, MVC, jquery, web forms" />
			<wordGroup name="Old Yuck Words" type="HIT" weight="-10" words="Access 2007, Access, Office 2010, Office 2007, Word 2010, PowerPoint, Excel" />
			<wordGroup name="Old Yuck Words" type="HIT" weight="-10" caseSensitive="true" words="COM, COM+, DCOM" />
			<wordGroup name="Must haves" type="MISS" weight="-10" words="ReSharper, ASP.NET, jquery, SQL Server" />
	    </wordGroups>
	</resumeScoring>

**runFrequency:** The number of hours between running the application.

**wordGroup:** A collection of words to apply to a resume.

**title:** Used in reporting back on an applicants profile to summarize the score generated by a wordGroup.

**type:** How to apply a rule for this set of words.

**weight:** The weight to apply to each word instance for that type matching.  Tracked by group.  Applied as a sum to the total score of the resume.

**words:** The words in a group for that filter type and weighting.

**caseSensitive:** Do we perform a case insensitive match or case sensitive?  This is important in some cases as COM should not match instances of www.myprojectsite.com but should match COM programming.

### Can be a json file in a cloud share location.

The configuration file might be stored in a blob store in Azure.

### This might end up being an azure worker role.

As this process needs to be run with some daily frequency it should be ran as a windows services or worker role.

## 2 - Open source C# Resumator API wrapper 

The outcome of this application should be a fairly useable component for interacting with the Resumator API.
 
