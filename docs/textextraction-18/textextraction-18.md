# Textextraction-18
The content-related task of the text extraction-18 was to extract all speeches of the 1st - 18th legislative period,
the XML documents passed by the crawler and output them in JSON files.
files. The crawler achieved this by temporarily persisting the plenary minutes as
as XML files and passing them as parameters to the text extraction-18.<br><br>

The basic idea is to examine the text block semantically and syntactically in order to find a search algorithm.
algorithm, which will find the text within the text block:<br>
&#9679; Title of the speech &emsp;&emsp; &#9679; Name of the speaker &emsp;&emsp; &#9679; Affiliation &emsp;&emsp; &#9679; Date of the speech &emsp;&emsp; &#9679; Speech<br>
to a person and extracts it.<br><br>
<hr>