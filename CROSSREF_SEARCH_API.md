##CROSSREF SEARCH API

Following are the crossref APIs being used in Chorus search application:


###Landing Page:

#### US Federal Only Unchecked:

As user types in text into the search box, ajax calls to following API are made:

http://api.crossref.org/v1/funders?rows=1000&query=[text_entered_by_user]

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

![](https://dl-web.dropbox.com/get/Chorus/search_ajax.png?_subject_uid=294139901&w=AADY7Y-r2D310IVBqRbsdVYl-IywBM93HgTDiJHIvyEdNQ)

#### US Federal Only Checked:

US only funders are queried from Funder IDS application. Funder IDS application queries crossref API for some top level funders. 

The top-level funders queried by Funder IDs application are:

[100000133, 100000030, 100000180,
 100000005, 100000015, 100000201,
 100000140, 100000138, 100000139,
 100000038, 100000104, 100000002,
 100000161, 100000192, 100000001, 100000014, 100000200, 100000199,
 100000738]

The crossref API called is:

http://api.crossref.org/v1/funders/10.13039/:funder_id

Example:

http://api.crossref.org/v1/funders/10.13039/100000005

Funder IDs application makes API call for each of the top level funder and result is processed to form a json array containing funder name and id for each of the funder which is then returned to search application.

The funder ID API called by search application is:

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

Data is queried from two crossRef APIs and displayed on search results page. The APIs called are:

-  #### /funders/:funder_id

Example API call for funder `100000005`:

`
http://api.crossref.org/v1/funders/100000005
`

This call will return following data:

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
    "work-count": 260,
    "descendant-work-count": 3669,
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

Fields returned in response:
- descendants: Descendants is an array of all possible sub-organizations of queried funder
- hierarchy: An object containing ids of direct descendants of queried funder
- hierarchy-names: An object having id and funder name mapping for direct descendants of queried funder
- name: Name of queried funder
- location: Location of queried funder

The `hierarchy` and `hierarchy-names` fields are used for rendering the section of search results page highlighted below:

![](https://dl-web.dropbox.com/get/Chorus/hierarchy.png?_subject_uid=294139901&w=AAAdTia3J0FkYu2NehOmmi31N_YmiBnaldUvNYxjphupNQ)


- #### /funders/:funder_id/works

Second call is made to the following API:

`
http://api.crossref.org/v1/funders/:funder_id/works
`

The query parameters passed with above URL are:
- facet: Setting facet to 't' enables facet information in response
- rows: Number of results per request
- filter: Used for filtering results by speciific field. Results are filtered by crossRef members


Example API call for funder `100000005`:

http://api.crossref.org/v1/funders/100000005/works?facet=t&rows=20&filter=member:15,member:16,member:78,member:179,member:221,member:221,member:263,member:286,member:292,member:301,member:311,member:316,member:317,member:320


Data returned by above API call:

```
{
  "status": "ok",
  "message-type": "work-list",
  "message-version": "1.0.0",
  "message": {
    "items": [
      {
        "subtitle": [
          "Effect of Cognitive Load on Decision Making"
        ],
        "subject": [
          "Safety, Risk, Reliability and Quality",
          "Physiology (medical)"
        ],
        "issued": {
          "date-parts": [
            [
              2013,
              12,
              19
            ]
          ]
        },
        "score": 1.0,
        "prefix": "http:\/\/id.crossref.org\/prefix\/10.1111",
        "author": [
          {
            "family": "Allen",
            "given": "Pamela M."
          },
          {
            "family": "Edwards",
            "given": "John A."
          },
          {
            "family": "Snyder",
            "given": "Frank J."
          },
          {
            "family": "Makinson",
            "given": "Kevin A."
          },
          {
            "family": "Hamby",
            "given": "David M."
          }
        ],
        "container-title": [
          "Risk Analysis"
        ],
        "reference-count": 28,
        "page": "1495-1505",
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
              "HDTRA108-10044"
            ],
            "name": "Defense Threat Reduction Agency",
            "DOI": "10.13039\/100000774"
          }
        ],
        "title": [
          "The Effect of Cognitive Load on Decision Making with Graphically Displayed Uncertainty Information"
        ],
        "type": "journal-article",
        "DOI": "10.1111\/risa.12161",
        "ISSN": [
          "0272-4332"
        ],
        "URL": "http:\/\/dx.doi.org\/10.1111\/risa.12161",
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
          "timestamp": 1409618028841
        },
        "volume": "34",
        "member": "http:\/\/id.crossref.org\/member\/311"
      },
	..............................
    ..............................
    ],
    "total-results": 2830,
    "query": {
      "search-terms": null,
      "start-index": 0
    },
    "items-per-page": 20,
    "facets": {
      "license": {
        "value-count": 16,
        "values": {
          "http:\/\/link.aps.org\/licenses\/aps-default-text-mining-license": 618,
          "http:\/\/link.aps.org\/licenses\/aps-default-license": 618,
          "http:\/\/link.aps.org\/licenses\/aps-default-accepted-manuscript-license": 618,
          "http:\/\/www.elsevier.com\/tdm\/userlicense\/1.0\/": 227,
          "http:\/\/pubs.acs.org\/page\/policy\/authorchoice_termsofuse.html": 79,
          "http:\/\/creativecommons.org\/licenses\/by\/3.0\/deed.en_US": 66,
          "http:\/\/www.sciencemag.org\/site\/feature\/contribinfo\/prep\/license.xhtml": 25,
          "http:\/\/creativecommons.org\/licenses\/by\/4.0\/": 9,
          "http:\/\/www.elsevier.com\/open-access\/userlicense\/1.0\/": 7,
          "http:\/\/www.acm.org\/publications\/policies\/copyright_policy#Background": 6,
          "http:\/\/www.acs.org\/content\/acs\/en\/copyright.html": 5,
          "http:\/\/pubs.acs.org\/userimages\/ContentEditor\/1388526979973\/authorchoice_form.pdf": 5,
          "http:\/\/www.ieee.org\/publications_standards\/publications\/subscriptions\/info\/licensing.html": 4,
          "http:\/\/pubs.acs.org\/page\/policy\/authorchoice_ccby_termsofuse.html": 3,
          "http:\/\/creativecommons.org\/licenses\/by-nc\/3.0": 2,
          "http:\/\/creativecommons.org\/licenses\/by\/3.0\/": 1
        }
      },
      "archive": {
        "value-count": 3,
        "values": {
          "Portico": 689,
          "DWT": 618,
          "CLOCKSS": 124
        }
      },
      "funder-doi": {
        "value-count": 20,
        "values": {
          "http:\/\/dx.doi.org\/10.13039\/100000001": 976,
          "http:\/\/dx.doi.org\/10.13039\/100000006": 891,
          "http:\/\/dx.doi.org\/10.13039\/100000181": 852,
          "http:\/\/dx.doi.org\/10.13039\/100000183": 616,
          "http:\/\/dx.doi.org\/10.13039\/100000185": 485,
          "http:\/\/dx.doi.org\/10.13039\/100000015": 297,
          "http:\/\/dx.doi.org\/10.13039\/100000005": 183,
          "http:\/\/dx.doi.org\/10.13039\/100000002": 127,
          "http:\/\/dx.doi.org\/10.13039\/100000774": 85,
          "http:\/\/dx.doi.org\/10.13039\/100006151": 61,
          "http:\/\/dx.doi.org\/10.13039\/501100001809": 45,
          "http:\/\/dx.doi.org\/10.13039\/100000078": 44,
          "http:\/\/dx.doi.org\/10.13039\/100000928": 33,
          "http:\/\/dx.doi.org\/10.13039\/100000104": 28,
          "http:\/\/dx.doi.org\/10.13039\/100000161": 26,
          "http:\/\/dx.doi.org\/10.13039\/100000165": 19,
          "http:\/\/dx.doi.org\/10.13039\/100000054": 19,
          "http:\/\/dx.doi.org\/10.13039\/100000182": 17,
          "http:\/\/dx.doi.org\/10.13039\/100000090": 17,
          "http:\/\/dx.doi.org\/10.13039\/100000057": 15
        }
      },
      "issn": {
        "value-count": 20,
        "values": {
          "http:\/\/id.crossref.org\/issn\/1079-7114": 575,
          "http:\/\/id.crossref.org\/issn\/0031-9007": 575,
          "http:\/\/id.crossref.org\/issn\/0003-6951": 410,
          "http:\/\/id.crossref.org\/issn\/0021-8979": 198,
          "http:\/\/id.crossref.org\/issn\/0021-9606": 163,
          "http:\/\/id.crossref.org\/issn\/1077-3118": 104,
          "http:\/\/id.crossref.org\/issn\/1520-5126": 93,
          "http:\/\/id.crossref.org\/issn\/0002-7863": 93,
          "http:\/\/id.crossref.org\/issn\/1530-6992": 69,
          "http:\/\/id.crossref.org\/issn\/1530-6984": 69,
          "http:\/\/id.crossref.org\/issn\/1936-086X": 68,
          "http:\/\/id.crossref.org\/issn\/1936-0851": 68,
          "http:\/\/id.crossref.org\/issn\/1089-7550": 60,
          "http:\/\/id.crossref.org\/issn\/1070-6631": 55,
          "http:\/\/id.crossref.org\/issn\/1932-7455": 48,
          "http:\/\/id.crossref.org\/issn\/1932-7447": 48,
          "http:\/\/id.crossref.org\/issn\/1550-235X": 42,
          "http:\/\/id.crossref.org\/issn\/1098-0121": 42,
          "http:\/\/id.crossref.org\/issn\/0034-6748": 42,
          "http:\/\/id.crossref.org\/issn\/1089-7690": 37
        }
      },
      "funder-name": {
        "value-count": 20,
        "values": {
          "National Science Foundation": 977,
          "Office of Naval Research": 891,
          "Air Force Office of Scientific Research": 852,
          "Army Research Office": 616,
          "Defense Advanced Research Projects Agency": 485,
          "U.S. Department of Energy": 298,
          "U.S. Department of Defense": 183,
          "National Institutes of Health": 127,
          "Defense Threat Reduction Agency": 85,
          "Basic Energy Sciences": 61,
          "National Natural Science Foundation of China": 47,
          "Division of Materials Research": 44,
          "Welch Foundation": 33,
          "National Aeronautics and Space Administration": 28,
          "National Institute of Standards and Technology": 26,
          "National Cancer Institute": 19,
          "Division of Chemistry": 19,
          "Medical Research and Materiel Command, U.S. Army Medical Department": 17,
          "Congressionally Directed Medical Research Programs": 17,
          "National Institute of General Medical Sciences": 15
        }
      },
      "container-title": {
        "value-count": 20,
        "values": {
          "Physical Review Letters": 575,
          "Phys. Rev. Lett.": 575,
          "Applied Physics Letters": 410,
          "Appl. Phys. Lett.": 410,
          "Journal of Applied Physics": 198,
          "J. Appl. Phys.": 198,
          "The Journal of Chemical Physics": 163,
          "J. Chem. Phys.": 163,
          "Journal of the American Chemical Society": 93,
          "J. Am. Chem. Soc.": 93,
          "Nano Letters": 69,
          "Nano Lett.": 69,
          "ACS Nano": 68,
          "Physics of Fluids": 55,
          "Phys. Fluids": 55,
          "The Journal of Physical Chemistry C": 48,
          "J. Phys. Chem. C": 48,
          "Rev. Sci. Instrum.": 42,
          "Physical Review B": 42,
          "Phys. Rev. B": 42
        }
      },
      "published": {
        "value-count": 7,
        "values": {
          "2014": 1337,
          "2011": 551,
          "2012": 530,
          "2013": 360,
          "2015": 28,
          "2010": 20,
          "2008": 2
        }
      },
      "category-name": {
        "value-count": 20,
        "values": {
          "Physics and Astronomy(all)": 1036,
          "Physics and Astronomy (miscellaneous)": 422,
          "Physical and Theoretical Chemistry": 311,
          "Condensed Matter Physics": 277,
          "Chemistry(all)": 245,
          "Materials Science(all)": 240,
          "Biochemistry": 129,
          "Materials Chemistry": 128,
          "Electronic, Optical and Magnetic Materials": 128,
          "Electrical and Electronic Engineering": 114,
          "Mechanical Engineering": 103,
          "Colloid and Surface Chemistry": 98,
          "Catalysis": 97,
          "Bioengineering": 95,
          "Engineering(all)": 83,
          "Surfaces, Coatings and Films": 80,
          "Applied Mathematics": 62,
          "Instrumentation": 52,
          "Computer Science Applications": 51,
          "Energy(all)": 49
        }
      },
      "source": {
        "value-count": 1,
        "values": {
          "CrossRef": 2830
        }
      },
      "orcid": {
        "value-count": 20,
        "values": {
          "http:\/\/orcid.org\/0000-0002-8845-9400": 2,
          "http:\/\/orcid.org\/0000-0002-1487-3182": 1,
          "http:\/\/orcid.org\/0000-0002-0774-7583": 1,
          "http:\/\/orcid.org\/0000-0002-0517-366X": 1,
          "http:\/\/orcid.org\/0000-0002-0381-1437": 1,
          "http:\/\/orcid.org\/0000-0002-0012-7963": 1,
          "http:\/\/orcid.org\/0000-0001-9931-906X": 1,
          "http:\/\/orcid.org\/0000-0001-9627-9074": 1,
          "http:\/\/orcid.org\/0000-0001-9076-3551": 1,
          "http:\/\/orcid.org\/0000-0001-8526-5152": 1,
          "http:\/\/orcid.org\/0000-0001-8200-1733": 1,
          "http:\/\/orcid.org\/0000-0001-7622-6477": 1,
          "http:\/\/orcid.org\/0000-0001-6491-167X": 1,
          "http:\/\/orcid.org\/0000-0001-6477-2542": 1,
          "http:\/\/orcid.org\/0000-0001-6470-4689": 1,
          "http:\/\/orcid.org\/0000-0001-6298-0874": 1,
          "http:\/\/orcid.org\/0000-0001-6215-6383": 1,
          "http:\/\/orcid.org\/0000-0001-5984-4409": 1,
          "http:\/\/orcid.org\/0000-0001-5386-3748": 1,
          "http:\/\/orcid.org\/0000-0001-5372-510X": 1
        }
      },
      "publisher-name": {
        "value-count": 11,
        "values": {
          "AIP Publishing": 939,
          "American Physical Society (APS)": 674,
          "American Chemical Society (ACS)": 621,
          "Elsevier BV": 257,
          "Wiley-Blackwell": 188,
          "Institute of Electrical & Electronics Engineers (IEEE)": 114,
          "American Association for the Advancement of Science (AAAS)": 25,
          "Association for Computing Machinery (ACM)": 6,
          "American Psychological Association (APA)": 3,
          "Oxford University Press (OUP)": 2,
          "Informa UK Limited": 1
        }
      },
      "type-name": {
        "value-count": 1,
        "values": {
          "Journal Article": 2830
        }
      }
    }
  }
}
```

Feilds returned in response:

- items: Array of works metadata associated with queried `funder_id`
- total-results: Count of works associated with queried `funder_id`
- facets: An object containing data of facets like `license`, `archive`, `funder-doi`, `issn`, `funder-name`, `container-title`, `published`, `category-name`, `source`, `orcid`, `publisher-name` and `type-name`.

Data from `items` and `total-results` fields are displayed in highlighted section below:

![](https://dl-web.dropbox.com/get/Chorus/results.png?_subject_uid=294139901&w=AACfXWGFQtDAaaooyR5kAFfzeV1BX0WmTjSzJKRrSQCtPA)