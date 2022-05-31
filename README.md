# IAP
In App Purchase for iOS

//
//  ViewController.m
//  IAP
//
//  Created by Haizel on 2022/5/31.
//

#import "ViewController.h"
#import <StoreKit/StoreKit.h>


@interface ViewController () <SKProductsRequestDelegate, SKPaymentTransactionObserver>

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    /*
     1、从苹果商店获取商品详情
     2、并且获取对应商品、连接苹果商店
     3、发起内购
     4、处理内购结果
     */
    
    NSSet *productIds = [[NSSet alloc] initWithArray:@[@"yyvoice.iap.0.7YCoins.2",@"yyvoice.iap.4.2YCoins.2"]];
    
    if ([SKPaymentQueue canMakePayments]) {
        [self requestIAPProducts:productIds];
        
        [[SKPaymentQueue defaultQueue] addTransactionObserver:self];
        
    } else {
        
    }
}


- (void)requestIAPProducts:(NSSet<NSString *> *)productIds
{
    SKProductsRequest *productReq = [[SKProductsRequest alloc] initWithProductIdentifiers:productIds];
    productReq.delegate = self;
    [productReq start];
}

#pragma mark SKProductsRequestDelegate
- (void)productsRequest:(SKProductsRequest *)request didReceiveResponse:(SKProductsResponse *)response
{
    
    [response.products enumerateObjectsUsingBlock:^(SKProduct * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        
    }];
    
    [response.invalidProductIdentifiers enumerateObjectsUsingBlock:^(NSString * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        
    }];
    
    if (response.products.count > 0) {
        
        SKProduct *product = response.products.firstObject;
        SKMutablePayment *payment = [[SKMutablePayment alloc] init];
        payment.applicationUsername = @"uid";
        payment.productIdentifier    = product.productIdentifier;
        payment.quantity             = 1;
        
        [[SKPaymentQueue defaultQueue] addPayment:payment];
        
    }
}
- (void)requestDidFinish:(SKRequest *)request
{
    
}
- (void)request:(SKRequest *)request didFailWithError:(NSError *)error
{
    
}

#pragma mark SKPaymentTransactionObserver
- (void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray<SKPaymentTransaction *> *)transactions
{
    for (SKPaymentTransaction *transaction in transactions) {
       
        switch (transaction.transactionState) {
                // 1、购买成功
                // 2、购买成功，但是你没有finish掉，没有消耗
            case SKPaymentTransactionStatePurchased: {
             
                //自动订阅订单抛出，只有自动订阅订单originalTransaction才会有值
                if (transaction.originalTransaction) {
                   
                } else {
                  
                }
                // 对于成功的订单需要先到后台校验 成功后才可以消单
//                [[SKPaymentQueue defaultQueue] finishTransaction:transaction];
                
                break;
            }
            case SKPaymentTransactionStateFailed: {
                
                [[SKPaymentQueue defaultQueue] finishTransaction:transaction];
               
                break;
            }
            case SKPaymentTransactionStateRestored: {
                
//                [[SKPaymentQueue defaultQueue] finishTransaction:transaction];
               
                break;
            }
            case SKPaymentTransactionStatePurchasing: {
                break;
            }
            case SKPaymentTransactionStateDeferred: {
                break;
            }
            default: {
                break;
            }
        }
    }
}

@end
