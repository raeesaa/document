##CROSSREF SEARCH API

Following are the crossref APIs being used in Chorus search application:


###Landing Page:

#### US Federal Only Unchecked:

As user types in text into the search box, ajax calls to following API are made:

http://api.crossref.org/v1/funders?rows=1000&query=<text_entered_by_user>

This API returns an array of funders having queried text in their name.

Example: 
http://api.crossref.org/v1/funders?rows=1000&query=national

The data returned by above call is:

```
{
  "status": "ok",
  "message-type": "funder-list",
  "message-version": "1.0.0",
  "message": {
    "items": [
      {
        "id": "501100000305",
        "location": "United Kingdom",
        "name": "National Eye Research Centre",
        "alt-names": [
          "NERC"
        ],
        "uri": "http:\/\/dx.doi.org\/10.13039\/501100000305",
        "tokens": [
          "national",
          "eye",
          "research",
          "centre",
          "nerc"
        ]
      },
      ...............
      ...............
    ],
    "total-results": 397,
    "query": {
      "search-terms": "national",
      "start-index": 0
    },
    "items-per-page": 1000
  }
}
```

Here, items is an array of agency names. These agency names are dispayed in jquery dropdown.

#### US Federal Only Checked:

US only funders are queried from Funder IDS application which queries crossref API for some top level funders. The crossref API called is:

http://api.crossref.org/v1/funders/10.13039/:funder_id

Example:
http://api.crossref.org/v1/funders/10.13039/100000005

The data returned by above API is:

```
{
  "status": "ok",
  "message-type": "funder",
  "message-version": "1.0.0",
  "message": {
    "descendants": [
      "100000774",
      "100005713",
      "100000266",
      "100005712",
      "100006370",
      "100006751",
      "100006393",
      "100000181",
      "100000183",
      "100005711",
      "100000182",
      "100000090",
      "100000185",
      "100006394",
      "100000006",
      "100006769",
      "100006753",
      "100006752",
      "100007485",
      "100006965",
      "100007297"
    ],
    "name": "U.S. Department of Defense",
    "location": "United States",
    "hierarchy": {
      "100000005": {
        "100000006": {"more": true},
        "100000182": {"more": true},
        "100000181": {},
        "100000183": {},
        "100000185": {},
        "100000774": {},
        "100006370": {},
        "100006394": {},
        "100005712": {},
        "100006751": {"more": true},
        "100006393": {},
        "100005713": {},
        "100000090": {},
        "100005711": {},
        "100000266": {}
      }
    },
    "hierarchy-names": {
      "100000005": "U.S. Department of Defense",
      "100000006": "Office of Naval Research",
      "100000182": "Medical Research and Materiel Command, U.S. Army Medical Department",
      "100000181": "Air Force Office of Scientific Research",
      "100000183": "Army Research Office",
      "more": null,
      "100000185": "Defense Advanced Research Projects Agency",
      "100000774": "Defense Threat Reduction Agency",
      "100006370": "Small Business Innovation Research",
      "100006394": "Air Force Material Command",
      "100005712": "Missile Defense Agency",
      "100006751": "U.S. Army",
      "100006393": "Air Force Civil Engineer Center",
      "100005713": "Office of the Secretary of Defense",
      "100000090": "Congressionally Directed Medical Research Programs",
      "100005711": "Defense Logistics Agency",
      "100000266": "National Geospatial-Intelligence Agency"
    },
    "uri": "http:\/\/dx.doi.org\/10.13039\/100000005",
    "work-count": 259,
    "descendant-work-count": 3651,
    "id": "100000005",
    "tokens": [
      "us",
      "department",
      "of",
      "defense",
      "dod"
    ],
    "alt-names": [
      "DOD"
    ]
  }
}
```

The top-level funders queried by Funder IDs application are:
[100000133, 100000030, 100000180,
 100000005, 100000015, 100000201,
 100000140, 100000138, 100000139,
 100000038, 100000104, 100000002,
 100000161, 100000192, 100000001, 100000014, 100000200, 100000199,
 100000738]

Note: Above top level funders are hardcoded in the code.

Funder IDs application makes API call for each of the top level funder and result is processed to form a json array containing funder name and id for each of the funder. Search application makes a call to following API of funders application

`
/funders_list_ids.json
`

which returns data of the form:

```
[
  {
    "name": "Agency for Healthcare Research and Quality",
    "id": 100000133
  },
  {
    "name": "Centers for Disease Control and Prevention",
    "id": 100000030
  },
  {
    "name": "National Center for Chronic Disease Prevention and Health Promotion",
    "id": "100000130"
  },
  {
    "name": "National Institute for Occupational Safety and Health",
    "id": "100000125"
  },
  {
    "name": "National Center for Preparedness, Detection, and Control of Infectious Diseases",
    "id": "100005218"
  }
  .................
  .................
 ]
```

Response is then parsed and funder names are displayed in jquery dropdown matching the text user types in.

###Search Results Page:

The data displayed in search results page is fetched from following API:

http://api.crossref.org/v1/funders/:funder_id/works?facet=t&rows=20&filter=member:15,member:16,member:78,member:179,member:221,member:221,member:263,member:286,member:292,member:301,member:311,member:316,member:317,member:320

where :funder_id corresponds to the id of funder the search is being performed for.

Query parameters used:
facet: Setting facet to 't' enables facet information in response
rows: Number of results per request
filter: Used for filtering results by specific field

The members sent in request as filter parameter were hardcoded in code.

Example:

http://api.crossref.org/v1/funders/100000016/works?facet=t&rows=20&filter=member:15,member:16,member:78,member:179,member:221,member:221,member:263,member:286,member:292,member:301,member:311,member:316,member:317,member:320


Sample data returned by above API:

```
{
  "status": "ok",
  "message-type": "work-list",
  "message-version": "1.0.0",
  "message": {
    "items": [
      {
        "subtitle": [
          "Models to Predict Facemask Influenza Contamination"
        ],
        "subject": [
          "Safety, Risk, Reliability and Quality",
          "Physiology (medical)"
        ],
        "issued": {
          "date-parts": [
            [
              2014,
              3,
              4
            ]
          ]
        },
        "score": 1.0,
        "prefix": "http:\/\/id.crossref.org\/prefix\/10.1111",
        "author": [
          {
            "family": "Fisher",
            "given": "Edward M."
          },
          {
            "family": "Noti",
            "given": "John D."
          },
          {
            "family": "Lindsley",
            "given": "William G."
          },
          {
            "family": "Blachere",
            "given": "Francoise M."
          },
          {
            "family": "Shaffer",
            "given": "Ronald E."
          }
        ],
        "container-title": [
          "Risk Analysis"
        ],
        "reference-count": 63,
        "page": "1423-1434",
        "deposited": {
          "date-parts": [
            [
              2014,
              8,
              29
            ]
          ],
          "timestamp": 1409270400000
        },
        "issue": "8",
        "funder": [
          {
            "award": [
              
            ],
            "name": "National Institute for Occupational Safety and Health",
            "DOI": "10.13039\/100000125"
          }
        ],
        "title": [
          "Validation and Application of Models to Predict Facemask Influenza Contamination in Healthcare Settings"
        ],
        "type": "journal-article",
        "DOI": "10.1111\/risa.12185",
        "ISSN": [
          "0272-4332"
        ],
        "URL": "http:\/\/dx.doi.org\/10.1111\/risa.12185",
        "source": "CrossRef",
        "publisher": "Wiley-Blackwell",
        "indexed": {
          "date-parts": [
            [
              2014,
              9,
              2
            ]
          ],
          "timestamp": 1409618168883
        },
        "volume": "34",
        "member": "http:\/\/id.crossref.org\/member\/311"
      }
  ],
    "total-results": 7798,
    "query": {
      "search-terms": null,
      "start-index": 0
    },
    "items-per-page": 20,
    "facets": {
      "license": {
        "value-count": 17,
        "values": {
          "http:\/\/www.elsevier.com\/tdm\/userlicense\/1.0\/": 1743,
          "http:\/\/pubs.acs.org\/page\/policy\/authorchoice_termsofuse.html": 1425,
          "http:\/\/pubs.acs.org\/userimages\/ContentEditor\/1388526979973\/authorchoice_form.pdf": 317,
          "http:\/\/www.elsevier.com\/open-access\/userlicense\/1.0\/": 94,
          "http:\/\/www.sciencemag.org\/site\/feature\/contribinfo\/prep\/license.xhtml": 80,
          "http:\/\/link.aps.org\/licenses\/aps-default-text-mining-license": 65,
          "http:\/\/link.aps.org\/licenses\/aps-default-license": 65,
          "http:\/\/link.aps.org\/licenses\/aps-default-accepted-manuscript-license": 65,
          "http:\/\/creativecommons.org\/licenses\/by-nc\/3.0": 45,
          "http:\/\/creativecommons.org\/licenses\/by\/3.0\/deed.en_US": 26,
          "http:\/\/www.acs.org\/content\/acs\/en\/copyright.html": 15,
          "http:\/\/olabout.wiley.com\/WileyCDA\/Section\/id-815641.html": 10,
          "http:\/\/www.tandfonline.com\/page\/terms-and-conditions#link2": 7,
          "http:\/\/pubs.acs.org\/page\/policy\/authorchoice_ccby_termsofuse.html": 5,
          "http:\/\/www.ieee.org\/publications_standards\/publications\/subscriptions\/info\/licensing.html": 2,
          "http:\/\/www.acm.org\/publications\/policies\/copyright_policy#Background": 2,
          "http:\/\/creativecommons.org\/licenses\/by\/3.0\/": 1
        }
      },
      "archive": {
        "value-count": 3,
        "values": {
          "DWT": 383,
          "Portico": 108,
          "CLOCKSS": 49
        }
      },
      "funder-doi": {
        "value-count": 20,
        "values": {
          "http:\/\/dx.doi.org\/10.13039\/100000002": 4894,
          "http:\/\/dx.doi.org\/10.13039\/100000001": 830,
          "http:\/\/dx.doi.org\/10.13039\/100000057": 608,
          "http:\/\/dx.doi.org\/10.13039\/100000054": 517,
          "http:\/\/dx.doi.org\/10.13039\/100000025": 330,
          "http:\/\/dx.doi.org\/10.13039\/100000071": 209,
          "http:\/\/dx.doi.org\/10.13039\/100000026": 203,
          "http:\/\/dx.doi.org\/10.13039\/100000060": 199,
          "http:\/\/dx.doi.org\/10.13039\/100000052": 191,
          "http:\/\/dx.doi.org\/10.13039\/100000062": 186,
          "http:\/\/dx.doi.org\/10.13039\/100000015": 186,
          "http:\/\/dx.doi.org\/10.13039\/100000097": 175,
          "http:\/\/dx.doi.org\/10.13039\/100000065": 170,
          "http:\/\/dx.doi.org\/10.13039\/100000049": 157,
          "http:\/\/dx.doi.org\/10.13039\/100000066": 153,
          "http:\/\/dx.doi.org\/10.13039\/100000050": 143,
          "http:\/\/dx.doi.org\/10.13039\/100000968": 105,
          "http:\/\/dx.doi.org\/10.13039\/100006108": 95,
          "http:\/\/dx.doi.org\/10.13039\/100000070": 87,
          "http:\/\/dx.doi.org\/10.13039\/100000030": 87
        }
      },
      "issn": {
        "value-count": 20,
        "values": {
          "http:\/\/id.crossref.org\/issn\/1520-5126": 441,
          "http:\/\/id.crossref.org\/issn\/0002-7863": 441,
          "http:\/\/id.crossref.org\/issn\/1520-4995": 290,
          "http:\/\/id.crossref.org\/issn\/0006-2960": 290,
          "http:\/\/id.crossref.org\/issn\/0021-9606": 173,
          "http:\/\/id.crossref.org\/issn\/1520-6882": 146,
          "http:\/\/id.crossref.org\/issn\/0003-2700": 146,
          "http:\/\/id.crossref.org\/issn\/1554-8937": 121,
          "http:\/\/id.crossref.org\/issn\/1554-8929": 121,
          "http:\/\/id.crossref.org\/issn\/1523-7060": 105,
          "http:\/\/id.crossref.org\/issn\/1523-7052": 105,
          "http:\/\/id.crossref.org\/issn\/1520-4804": 99,
          "http:\/\/id.crossref.org\/issn\/0022-2623": 99,
          "http:\/\/id.crossref.org\/issn\/1936-086X": 86,
          "http:\/\/id.crossref.org\/issn\/1936-0851": 86,
          "http:\/\/id.crossref.org\/issn\/1535-3907": 80,
          "http:\/\/id.crossref.org\/issn\/1535-3893": 80,
          "http:\/\/id.crossref.org\/issn\/1095-9203": 80,
          "http:\/\/id.crossref.org\/issn\/0036-8075": 80,
          "http:\/\/id.crossref.org\/issn\/1520-5207": 74
        }
      },
      "funder-name": {
        "value-count": 20,
        "values": {
          "National Institutes of Health": 4895,
          "National Science Foundation": 832,
          "National Institute of General Medical Sciences": 608,
          "National Cancer Institute": 517,
          "National Institute of Mental Health": 331,
          "National Institute of Child Health and Human Development": 209,
          "National Institute on Drug Abuse": 203,
          "National Institute of Allergy and Infectious Diseases": 200,
          "NIH Office of the Director": 191,
          "U.S. Department of Energy": 186,
          "National Institute of Diabetes and Digestive and Kidney Diseases": 186,
          "National Center for Research Resources": 177,
          "National Institute of Neurological Disorders and Stroke": 172,
          "National Institute on Aging": 165,
          "National Institute of Environmental Health Sciences": 153,
          "National Heart, Lung, and Blood Institute": 145,
          "American Heart Association": 107,
          "National Center for Advancing Translational Sciences": 96,
          "National Natural Science Foundation of China": 88,
          "National Institute of Biomedical Imaging and Bioengineering": 88
        }
      },
      "container-title": {
        "value-count": 20,
        "values": {
          "Journal of the American Chemical Society": 441,
          "J. Am. Chem. Soc.": 441,
          "Biochemistry": 290,
          "The Journal of Chemical Physics": 173,
          "J. Chem. Phys.": 173,
          "Analytical Chemistry": 146,
          "Anal. Chem.": 146,
          "ACS Chemical Biology": 121,
          "ACS Chem. Biol.": 121,
          "Organic Letters": 105,
          "Org. Lett.": 105,
          "Journal of Medicinal Chemistry": 99,
          "J. Med. Chem.": 99,
          "ACS Nano": 86,
          "Science": 80,
          "Journal of Proteome Research": 80,
          "J. Proteome Res.": 80,
          "The Journal of Physical Chemistry B": 74,
          "J. Phys. Chem. B": 74,
          "Hepatology": 73
        }
      },
      "published": {
        "value-count": 12,
        "values": {
          "2014": 6561,
          "2013": 408,
          "2011": 367,
          "2012": 327,
          "2010": 78,
          "2015": 39,
          "2009": 8,
          "2008": 6,
          "2007": 1,
          "2006": 1,
          "2002": 1,
          "1978": 1
        }
      },
      "category-name": {
        "value-count": 20,
        "values": {
          "Biochemistry": 1543,
          "Chemistry(all)": 706,
          "Molecular Biology": 606,
          "Physical and Theoretical Chemistry": 504,
          "Colloid and Surface Chemistry": 446,
          "Catalysis": 442,
          "Molecular Medicine": 432,
          "Medicine(all)": 432,
          "Cell Biology": 369,
          "Organic Chemistry": 366,
          "Physics and Astronomy(all)": 328,
          "Psychiatry and Mental health": 323,
          "Neurology": 308,
          "Drug Discovery": 292,
          "Clinical Neurology": 262,
          "Genetics": 253,
          "Pharmacology": 251,
          "Pharmaceutical Science": 226,
          "Neuroscience(all)": 224,
          "Analytical Chemistry": 218
        }
      },
      "source": {
        "value-count": 1,
        "values": {
          "CrossRef": 7798
        }
      },
      "orcid": {
        "value-count": 20,
        "values": {
          "http:\/\/orcid.org\/0000-0001-7916-622X": 3,
          "http:\/\/orcid.org\/0000-0002-6921-9567": 2,
          "http:\/\/orcid.org\/0000-0002-6264-2573": 2,
          "http:\/\/orcid.org\/0000-0002-4064-1924": 2,
          "http:\/\/orcid.org\/0000-0002-3832-3249": 2,
          "http:\/\/orcid.org\/0000-0002-0738-8453": 2,
          "http:\/\/orcid.org\/0000-0001-7112-4009": 2,
          "http:\/\/orcid.org\/0000-0001-5420-3776": 1,
          "http:\/\/orcid.org\/0000-0001-5398-3408": 1,
          "http:\/\/orcid.org\/0000-0001-5388-897X": 1,
          "http:\/\/orcid.org\/0000-0001-5386-3748": 1,
          "http:\/\/orcid.org\/0000-0001-5372-510X": 1,
          "http:\/\/orcid.org\/0000-0001-5335-9840": 1,
          "http:\/\/orcid.org\/0000-0001-5315-401X": 1,
          "http:\/\/orcid.org\/0000-0001-5218-6722": 1,
          "http:\/\/orcid.org\/0000-0001-5158-1149": 1,
          "http:\/\/orcid.org\/0000-0001-5138-3839": 1,
          "http:\/\/orcid.org\/0000-0001-5059-0211": 1,
          "http:\/\/orcid.org\/0000-0001-5044-672X": 1,
          "http:\/\/orcid.org\/0000-0001-5010-7883": 1
        }
      },
      "publisher-name": {
        "value-count": 11,
        "values": {
          "Wiley-Blackwell": 3054,
          "American Chemical Society (ACS)": 2203,
          "Elsevier BV": 1896,
          "AIP Publishing": 330,
          "American Association for the Advancement of Science (AAAS)": 80,
          "American Physical Society (APS)": 67,
          "American Psychological Association (APA)": 63,
          "Institute of Electrical & Electronics Engineers (IEEE)": 49,
          "Oxford University Press (OUP)": 45,
          "Informa UK Limited": 9,
          "Association for Computing Machinery (ACM)": 2
        }
      },
      "type-name": {
        "value-count": 1,
        "values": {
          "Journal Article": 7798
        }
      }
    }
  }
}
```