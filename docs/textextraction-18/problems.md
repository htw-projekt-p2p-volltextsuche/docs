# Problems
Most of the problems arose from the assumption that there was consistency in the formatting of the documents.
<table>
	<tr>
		<th style="text-align:left">Problem</th>
		<th style="text-align:left">Description</th>
	</tr>
	<tr>
		<td>Document type</td>
		<td>In the 1st - 14th electoral legislature period, a document type was given to the XML files, but from the from the 15th electoral legislature onwards, this was no longer the case.</td>
	</tr>
	<tr>
		<td>Formatting</td>
		<td>In some documents, the considered commonalities of the table of contents were also not present, which makes a uniform programme for all electoral legislatures almost impossible.</td>
	</tr>
	<tr>
		<td>Naming of the affiliation</td>
		<td>There were also many spelling differences in the naming of affiliations. The best example of this is the party "BUNDNIS 90/DIE GRÜNEN". For the different spellings of this party alone:
		<ul>
			<li>BÜNDNIS 90/DIE GRÜNEN</li>
			<li>BÜNDNIS 90/<br>DIE GRÜNEN</li>
			<li>BÜNDNIS 90<br><br>/DIE GRÜNEN</li>
			<li>BÜNDNIS 90/DIE GRÜ-<br>NEN</li>
		</ul>
three regular printouts had to be changed to make them work.</td>
	</tr>
</table>
The decisive point in the extraction was the search in the table of contents for persons. Within the search itself, however, there were differences that had to be processed separately. The search was implemented with the method createMap() in the SpeechSearch class. The literal flow of the method is roughly as follows:

* ***IF*** there is a title
	* the first name is also contained in the same entry and must be saved<br>after that, each entry is a name,<br>until the entry that contains a title.<br>Next loop pass 	from this title.
* ***otherwise***
	* ***if*** a title is followed by a name
		* **then** each subsequent entry is a name and must be saved,<br>until an entry contains a title.<br>next loop pass from this title.

If the speakers were found for each title, these entries are saved in a map.
