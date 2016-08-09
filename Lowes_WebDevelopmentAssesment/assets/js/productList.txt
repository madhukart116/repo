var prodList = angular.module('productlist', []);

prodList.controller('listcontroller', function($scope, getProducts) {
  getProducts.getProductList().then(function(response) {
    $scope.data = response.data;
    $scope.productList = $scope.data.productList;
  });
});

prodList.factory('getProducts', function($http) {
  this.getProductList = function() {
    // .get thowing cross-browser compatibility errors so copied the json data from the URL and loaded with JSONP
    // return $http.get('http://m.lowes.com/CatalogServices/product/nvalue/v1_0?nValue=4294857975&maxResults=20&showURL=1&rollUpVariants=1&showUrl=true&storeNumber=0595&priceFlag=rangeBalance&showMarketingBullets=1')
    return $http.jsonp('./assets/test.json')
      .success(function(response) {
        return {
          data: response
        };
      });
  };

  return {
    getProductList: this.getProductList
  };
});

prodList.directive('product', function($sce) {
  return {
    restrict: 'E',
    scope: {
      prodInfo: '=info'
    },
    link: function(scope, ele, attr) {
      scope.showPriceAlert = function(price) {
          alert("The Price of the Product is "+price);
      };
    },
    template: ['<div class="item">',
             '<div class="item-img"><img src="{{prodInfo.imageUrls.sm}}" class="sm" alt="" /><img src="{{prodInfo.imageUrls.md}}" alt="" class="md" /></div>',
             '<div class="item-desc"><p>{{prodInfo.description}}</p><ul><li ng-repeat="list in prodInfo.marketingBullets" ng-bind-html="list | unsafe"></li></ul></div>',
             '<div class="addToCart"><button type="button" name="ADD TO CART" ng-click="showPriceAlert(prodInfo.pricing.price.selling)">Add to Cart</button>',
             '</div><div class="item-price">${{prodInfo.pricing.price.selling}}</div>',
             '<div class="view-more"><button type="button" name="View More">View More</button></div></div>'
           ].join('')
  };
});
prodList.filter('unsafe', function($sce) { return $sce.trustAsHtml; });
