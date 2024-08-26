# Easy-Custom-select2-search
Js easy select2 search

```javascript
<script src="https://code.jquery.com/jquery-3.6.0.js" integrity="sha256-H+K7U5CnXl1h5ywQfKtSj8PCmoN9aaq30gDh27Xc0jk=" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js"></script>
```

```javascript

function CustomSelect2Search({ url, elementID, multiple, placeholderText, dropdownParent, width, onUnselect, onClose, success, complete }) {
    var initialUrl = url;
    var nextPageUrl = initialUrl;

    $('#' + elementID).select2({
        dropdownParent: dropdownParent,
        width: width,
        ajax: {
            url: function (params) {
                let url = new URL(initialUrl, window.location.origin);
                if (params.term) {
                    url.searchParams.set('search', params.term); // Set page to 1 for search
                    url.searchParams.set('page', params.page || "1"); // Set page to 1 for search
                } else {
                    url.searchParams.set('page', params.page || "1")
                }
                nextPageUrl = url.toString();
                return nextPageUrl;
            },
            dataType: "json",
            delay: 250,
            processResults: function (res) {
                nextPageUrl = res.next_page_url ? res.next_page_url : initialUrl;
                let dataArray = res.data ? res.data : [];
                return {
                    results: dataArray.map(function (item) {
                        return {
                            id: item.id,
                            text: item.name,
                        };
                    }),
                    pagination: {
                        more: nextPageUrl !== initialUrl,
                    },
                };
            },
            cache: true,
            error: function (error) {
                console.error("Error fetching data:", error);
            },
            data: function (params) {
                //   if (params.term) {
                //       return {
                //           search: params.term || "",
                //       };
                //   }
                //   return;
            },
            success: success,
            complete: complete,
        },
        allowClear: true,
        escapeMarkup: function (markup) {
            return markup;
        },
        placeholder: placeholderText,
        multiple: multiple,
        initSelection: function (element, callback) {
            callback([]);
        },
        close: function () {
            nextPageUrl = initialUrl;
        },

    });

    // Attach custom event handlers if provided
    if (onUnselect) {
        $('#' + elementID).on("select2:unselect", onUnselect);
    }

    if (onClose) {
        $('#' + elementID).on("select2:close", onClose);
    }

    // Reset the nextPageUrl on close
    $('#' + elementID).on("select2:close", function (e) {
        nextPageUrl = initialUrl;
    });

    // $('#' + elementID).on('select2:opening', function (e) {
    //     $(this).data('select2').dropdown.$search.on('input', function () {
    //     });
    // });

}

```

```javascript
function unselectItem(elementID) {
    $('#' + elementID).val(null).trigger('change');
}
```


```javascript

function getSelectedText(elementID) {
    var selectedOption = $('#' + elementID).select2('data');
    if (selectedOption && selectedOption.length > 0) {
        return selectedOption[0].text;
    }
    return null;
}
```

## Usage

```javascript
ustomSelect2Search({
  url: 'your-api-url',
  elementID: 'add-store-issue-packing-control',
  multiple: true,
  placeholderText: 'Select an option',
  dropdownParent: '#your-dropdown-parent-element',
  width: '100%',
  onUnselect: function(e) {
      // your code
  },
  onClose: function(e) {
      // your code
  }
success: function (data) {
  console.log('API call successful with data:', data);

},
complete: function (jqXHR, textStatus) {
  console.log('API call completed with status:', textStatus);
  console.log('API call completed with status:', jqXHR);
  // Additional code can go here if needed
},
});

```
