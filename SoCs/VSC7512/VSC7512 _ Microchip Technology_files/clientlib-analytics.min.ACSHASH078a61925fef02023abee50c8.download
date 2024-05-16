/* add-data-attribute.js */

const clean_string = ((string) => {
    // trim whitespace and replace excessive spaces with single whitespace characters
    return string.trim().replace(/\s+/g, ' '); // normalize excessive whitespace;
})

document.addEventListener("DOMContentLoaded", function () {

    // find buttons
    const all_buttons = document.querySelectorAll('button');

    // process buttons
    for (let buttonIndex = 0; buttonIndex < all_buttons.length; buttonIndex++) {
        const this_button = all_buttons[buttonIndex];
        let button_label = "";

        if (this_button.getAttribute("title")) { // check title
            button_label = this_button.getAttribute("title").trim();
        } else if (this_button.textContent !== "") { // check inner text
            button_label = clean_string(this_button.textContent);
        } else if (this_button.parentElement.textContent.trim() !== "") { // check outer text
            button_label = clean_string(this_button.parentElement.textContent);
        } else { // generic label
            button_label = `${this_button.parentElement.classList} ${this_button.type}`;
        }

        this_button.setAttribute("data-name", button_label);
    }

    // find button links in cards
    const all_button_links = document.querySelectorAll('a[role="button"]');

    // process button links
    for (let buttonIndex = 0; buttonIndex < all_button_links.length; buttonIndex++) {

        const this_button_link = all_button_links[buttonIndex];
        let button_label = "";
        let card_title = "";

        if (this_button_link.getAttribute("title")) { // check title
            button_label = this_button_link.getAttribute("title").trim();
        } else if (this_button_link.textContent.trim() !== "") { // check inner text
            button_label = clean_string(this_button_link.textContent);
        } else { // generic label
            button_label = this_button_link.parentElement.classList;
        }

        const titleSelector = '*[class*="title"]';

        const findTitleFor = (targetParent) => {
            const title = $(this_button_link).parents(targetParent).first().find(titleSelector).first();
            return title;
        }

        // check for card title
        if (findTitleFor('.mchp-card-item').length > 0) { // check for card title
            card_title = findTitleFor('.mchp-card-item').text();
        } else if (findTitleFor('.isolatedimage').length > 0) { // isolated image title
            card_title = findTitleFor('.isolatedimage').text();
        } else if (findTitleFor('.videofeature').length > 0) { // video feature title
            card_title = findTitleFor('.videofeature').text();
        } else if (findTitleFor('.textimage').length > 0) { // text image title
            card_title = findTitleFor('.textimage').text();
        } else { // grab the first available title
            card_title = findTitleFor('.aem-Grid--12, td').text();
        }

        // remove extraneous whitespace
        card_title = clean_string(card_title);

        if (card_title !== "" && button_label !== "") {
            // render with divider
            this_button_link.setAttribute("data-name", `${card_title}|${button_label}`);
        } else {
            this_button_link.setAttribute("data-name", `${card_title}${button_label}`);
        }
    }
})
/* filter-data.js */

/**
 * Passes filter selections to adobeDataLayer object for collection by Adobe Analytics
 * @param {String} filterAction The type of action that the user initiated, usually "add" or "remove"
 * @param {String} filterListActive A conjoined string of all active search filter categories
 * @param {String} filterName The name of the clicked filter category
 * @param {String} filterType The name of the filter group
 */
const captureSearchFilters = (filterAction, filterListActive, filterName, filterType) => {

    adobeDataLayer.push({
        "event": "srpFilter",
        "filterData": {
            "filterAction": filterAction, // "add", "remove"
            "filterListActive": filterListActive, // all active filters
            "filterName": filterName, // clicked filter category
            "filterType": filterType // filter group
        }
    });

}

// track interaction with search filters
$(document).on('click', '.cList', function (e) {

    const filterAction = "add";
    const filterName = e.target.innerText;
    const filterType = filterName;
    const filterListActive = filterName;

    captureSearchFilters(filterAction, filterListActive, filterName, filterType);
    
})

$(document).on('click', '.subList', function (e) {

    const parentCategory = $(e.target).parents('.categoryListElement').first().attr('id');

    const filterAction = "add";
    const filterName = e.target.innerText;
    const filterType = parentCategory;
    const filterListActive = `${filterType}:${filterName}`;

    captureSearchFilters(filterAction, filterListActive, filterName, filterType);
    
})

$(document).on('change', '.nc-facet', function (e) {

    const filterAction = e.target.checked ? "add" : "remove";
    const filterName = e.target.value;
    const filterType = $(e.target).parents('.categoryListElement').first().attr('id');
    const activeFilters = [];

    for (let i=0;i<document.querySelectorAll('.nc-facet:checked').length;i++) {
        activeFilters.push(`${filterType}:${document.querySelectorAll('.nc-facet:checked')[i].value}`);
    }

    const filterListActive = activeFilters.join("|");
    
    captureSearchFilters(filterAction, filterListActive, filterName, filterType);
})
/* search-data.js */

let userSearchTerm;

if (typeof adobeDataLayer === "undefined") {
  window.adobeDataLayer = new Array();
}

/**
 * Passes search details to adobeDataLayer object for collection by Adobe Analytics
 * @param {Object} response API response object
 */
const captureSearchData = (response) => {
  adobeDataLayer.push({
    event: "search",
    searchData: {
      hasResults: response.TotalResults > 0 ? "Y" : "N",
      searchTerm: userSearchTerm,
      searchText: response.SearchTerm,
      searchType: userSearchTerm === response.SearchTerm ? "manual" : "auto",
    },
  });
};

document.addEventListener("DOMContentLoaded", function () {
  const siteSearchInput = document.getElementById("siteSearchInput");
  // if search input exists
  if (siteSearchInput !== null) {
    // capture the input text
    userSearchTerm = siteSearchInput.value;
    // update the variable on keyup for Adobe Analytics collection
    siteSearchInput.addEventListener("keyup", function (e) {
      userSearchTerm = siteSearchInput.value;
    });
  }
});

