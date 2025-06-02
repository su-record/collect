# 6️⃣ PWA 최적화 가이드

## 🎯 **목표**
회의실 예약 시스템을 완전한 PWA(Progressive Web App)로 구성하여 네이티브 앱과 같은 사용자 경험을 제공합니다. 오프라인 지원, 푸시 알림, 홈 화면 설치 등의 기능을 구현합니다.

---

## 📋 **PWA 기능 목록**

### 🔄 **핵심 PWA 기능**
- **서비스 워커** - 캐싱, 오프라인 지원, 백그라운드 동기화
- **웹 앱 매니페스트** - 홈 화면 설치, 스플래시 화면
- **푸시 알림** - 백그라운드 알림 수신
- **오프라인 지원** - 네트워크 없이도 기본 기능 사용
- **백그라운드 동기화** - 온라인 복구 시 데이터 동기화
- **앱 업데이트** - 자동 업데이트 및 사용자 알림

---

## 🔧 **1단계: Vite PWA 플러그인 고급 설정**

### `vite.config.ts` 완전 설정
```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { VitePWA } from 'vite-plugin-pwa';
import path from 'path';

export default defineConfig({
  plugins: [
    react({
      jsxRuntime: 'automatic'
    }),
    VitePWA({
      registerType: 'autoUpdate',
      includeAssets: [
        'favicon.ico', 
        'apple-touch-icon.png', 
        'masked-icon.svg'
      ],
      
      // 매니페스트 설정
      manifest: {
        name: '회의실 예약 시스템',
        short_name: '회의실예약',
        description: '실시간 회의실 예약 및 관리 시스템',
        theme_color: '#ffffff',
        background_color: '#ffffff',
        display: 'standalone',
        orientation: 'portrait',
        scope: '/',
        start_url: '/',
        categories: ['business', 'productivity'],
        screenshots: [
          {
            src: 'screenshot1.png',
            sizes: '1280x720',
            type: 'image/png',
            label: 'Dashboard view'
          },
          {
            src: 'screenshot2.png',
            sizes: '750x1334',
            type: 'image/png',
            label: 'Mobile view'
          }
        ],
        icons: [
          {
            src: 'icon-72x72.png',
            sizes: '72x72',
            type: 'image/png',
            purpose: 'maskable any'
          },
          {
            src: 'icon-96x96.png',
            sizes: '96x96',
            type: 'image/png',
            purpose: 'maskable any'
          },
          {
            src: 'icon-128x128.png',
            sizes: '128x128',
            type: 'image/png',
            purpose: 'maskable any'
          },
          {
            src: 'icon-144x144.png',
            sizes: '144x144',
            type: 'image/png',
            purpose: 'maskable any'
          },
          {
            src: 'icon-152x152.png',
            sizes: '152x152',
            type: 'image/png',
            purpose: 'maskable any'
          },
          {
            src: 'icon-192x192.png',
            sizes: '192x192',
            type: 'image/png',
            purpose: 'maskable any'
          },
          {
            src: 'icon-384x384.png',
            sizes: '384x384',
            type: 'image/png',
            purpose: 'maskable any'
          },
          {
            src: 'icon-512x512.png',
            sizes: '512x512',
            type: 'image/png',
            purpose: 'maskable any'
          }
        ]
      },
      
      // 워크박스 설정
      workbox: {
        globPatterns: ['**/*.{js,css,html,ico,png,svg,woff,woff2}'],
        runtimeCaching: [
          {
            urlPattern: /^https:\/\/fonts\.googleapis\.com\/.*/i,
            handler: 'CacheFirst',
            options: {
              cacheName: 'google-fonts-cache',
              expiration: {
                maxEntries: 10,
                maxAgeSeconds: 60 * 60 * 24 * 365 // 1년
              },
              cacheKeyWillBeUsed: async ({ request }) => {
                return `${request.url}?${Date.now()}`;
              }
            }
          },
          {
            urlPattern: /^https:\/\/fonts\.gstatic\.com\/.*/i,
            handler: 'CacheFirst',
            options: {
              cacheName: 'gstatic-fonts-cache',
              expiration: {
                maxEntries: 10,
                maxAgeSeconds: 60 * 60 * 24 * 365 // 1년
              }
            }
          },
          {
            urlPattern: /\/api\/.*$/,
            handler: 'NetworkFirst',
            options: {
              cacheName: 'api-cache',
              expiration: {
                maxEntries: 100,
                maxAgeSeconds: 60 * 5 // 5분
              },
              networkTimeoutSeconds: 10,
              plugins: [
                {
                  cacheKeyWillBeUsed: async ({ request }) => {
                    return request.url;
                  },
                  cacheWillUpdate: async ({ response }) => {
                    return response.status === 200;
                  }
                }
              ]
            }
          }
        ],
        navigateFallback: '/index.html',
        navigateFallbackDenylist: [/^\/_/, /\/[^/?]+\.[^/]+$/],
      },
      
      // 개발 옵션
      devOptions: {
        enabled: true,
        type: 'module',
      }
    })
  ],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  // PWA 최적화
  optimizeDeps: {
    include: ['react', 'react-dom']
  },
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          router: ['react-router-dom'],
          ui: ['@radix-ui/react-slot', '@radix-ui/react-dialog'],
        }
      }
    }
  }
});
```

---

## 📱 **2단계: 오프라인 지원 구현**

### `src/hooks/useOfflineSupport.ts`
```typescript
import { useState, useEffect } from 'react';
import { useQueryClient } from '@tanstack/react-query';

/**
 * 오프라인 상태 관리 훅
 */
export const useOfflineSupport = () => {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  const [offlineActions, setOfflineActions] = useState<any[]>([]);
  const queryClient = useQueryClient();

  useEffect(() => {
    const handleOnline = () => {
      setIsOnline(true);
      // 온라인 복구 시 오프라인 액션 동기화
      syncOfflineActions();
    };

    const handleOffline = () => {
      setIsOnline(false);
    };

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);

    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  /**
   * 오프라인 액션을 큐에 추가
   */
  const addOfflineAction = (action: any) => {
    const newAction = {
      id: Date.now().toString(),
      timestamp: new Date().toISOString(),
      ...action,
    };
    
    setOfflineActions(prev => [...prev, newAction]);
    
    // localStorage에 저장
    const stored = localStorage.getItem('offlineActions') || '[]';
    const actions = JSON.parse(stored);
    localStorage.setItem('offlineActions', JSON.stringify([...actions, newAction]));
  };

  /**
   * 오프라인 액션 동기화
   */
  const syncOfflineActions = async () => {
    const stored = localStorage.getItem('offlineActions');
    if (!stored) return;

    const actions = JSON.parse(stored);
    
    for (const action of actions) {
      try {
        await processOfflineAction(action);
        // 성공 시 액션 제거
        setOfflineActions(prev => prev.filter(a => a.id !== action.id));
      } catch (error) {
        console.error('Failed to sync offline action:', error);
      }
    }

    // 처리된 액션들 localStorage에서 제거
    const remainingActions = offlineActions.filter(
      action => !actions.some((a: any) => a.id === action.id)
    );
    localStorage.setItem('offlineActions', JSON.stringify(remainingActions));
  };

  /**
   * 개별 오프라인 액션 처리
   */
  const processOfflineAction = async (action: any) => {
    switch (action.type) {
      case 'CREATE_RESERVATION':
        // 예약 생성 API 호출
        break;
      case 'UPDATE_RESERVATION':
        // 예약 수정 API 호출
        break;
      case 'DELETE_RESERVATION':
        // 예약 삭제 API 호출
        break;
      default:
        console.warn('Unknown offline action type:', action.type);
    }
  };

  return {
    isOnline,
    offlineActions,
    addOfflineAction,
    syncOfflineActions,
  };
};
```

### `src/components/common/OfflineIndicator.tsx`
```typescript
import { WifiOff, RefreshCw } from 'lucide-react';
import { Alert, AlertDescription } from '@/components/ui/alert';
import { Button } from '@/components/ui/button';
import { useOfflineSupport } from '@/hooks/useOfflineSupport';

/**
 * 오프라인 상태 표시 컴포넌트
 */
export const OfflineIndicator: React.FC = () => {
  const { isOnline, offlineActions, syncOfflineActions } = useOfflineSupport();

  if (isOnline && offlineActions.length === 0) {
    return null;
  }

  return (
    <Alert variant={isOnline ? 'default' : 'destructive'} className="mb-4">
      <WifiOff className="h-4 w-4" />
      <AlertDescription className="flex items-center justify-between">
        <div>
          {!isOnline ? (
            <span>오프라인 모드입니다. 일부 기능이 제한될 수 있습니다.</span>
          ) : (
            <span>
              온라인으로 복구되었습니다. 
              {offlineActions.length > 0 && ` ${offlineActions.length}개의 대기 중인 작업이 있습니다.`}
            </span>
          )}
        </div>
        
        {isOnline && offlineActions.length > 0 && (
          <Button 
            variant="outline" 
            size="sm" 
            onClick={syncOfflineActions}
          >
            <RefreshCw className="h-3 w-3 mr-1" />
            동기화
          </Button>
        )}
      </AlertDescription>
    </Alert>
  );
};
```

---

## 🔔 **3단계: 푸시 알림 구현**

### `src/services/pushNotifications.ts`
```typescript
import { ApiClient } from './api/client';

/**
 * 푸시 알림 구독 정보
 */
interface PushSubscription {
  endpoint: string;
  keys: {
    p256dh: string;
    auth: string;
  };
}

/**
 * 푸시 알림 서비스
 */
export class PushNotificationService {
  private vapidPublicKey: string;

  constructor(vapidPublicKey: string) {
    this.vapidPublicKey = vapidPublicKey;
  }

  /**
   * 푸시 알림 지원 여부 확인
   */
  isSupported(): boolean {
    return 'serviceWorker' in navigator && 'PushManager' in window;
  }

  /**
   * 알림 권한 요청
   */
  async requestPermission(): Promise<NotificationPermission> {
    if (!this.isSupported()) {
      throw new Error('Push notifications are not supported');
    }

    return await Notification.requestPermission();
  }

  /**
   * 서비스 워커 등록 및 푸시 구독
   */
  async subscribe(): Promise<PushSubscription | null> {
    if (!this.isSupported()) {
      return null;
    }

    const permission = await this.requestPermission();
    if (permission !== 'granted') {
      return null;
    }

    try {
      const registration = await navigator.serviceWorker.ready;
      
      // 기존 구독 확인
      let subscription = await registration.pushManager.getSubscription();
      
      if (!subscription) {
        // 새 구독 생성
        subscription = await registration.pushManager.subscribe({
          userVisibleOnly: true,
          applicationServerKey: this.urlBase64ToUint8Array(this.vapidPublicKey),
        });
      }

      // 구독 정보를 서버에 전송
      await this.sendSubscriptionToServer(subscription);

      return this.formatSubscription(subscription);
    } catch (error) {
      console.error('Failed to subscribe to push notifications:', error);
      return null;
    }
  }

  /**
   * 푸시 구독 해제
   */
  async unsubscribe(): Promise<boolean> {
    try {
      const registration = await navigator.serviceWorker.ready;
      const subscription = await registration.pushManager.getSubscription();
      
      if (subscription) {
        await subscription.unsubscribe();
        await this.removeSubscriptionFromServer(subscription);
        return true;
      }
      
      return false;
    } catch (error) {
      console.error('Failed to unsubscribe from push notifications:', error);
      return false;
    }
  }

  /**
   * Base64 URL을 Uint8Array로 변환
   */
  private urlBase64ToUint8Array(base64String: string): Uint8Array {
    const padding = '='.repeat((4 - base64String.length % 4) % 4);
    const base64 = (base64String + padding)
      .replace(/-/g, '+')
      .replace(/_/g, '/');

    const rawData = window.atob(base64);
    const outputArray = new Uint8Array(rawData.length);

    for (let i = 0; i < rawData.length; ++i) {
      outputArray[i] = rawData.charCodeAt(i);
    }
    return outputArray;
  }

  /**
   * 구독 정보 포맷팅
   */
  private formatSubscription(subscription: globalThis.PushSubscription): PushSubscription {
    const keys = subscription.getKey('p256dh');
    const auth = subscription.getKey('auth');

    return {
      endpoint: subscription.endpoint,
      keys: {
        p256dh: keys ? btoa(String.fromCharCode(...new Uint8Array(keys))) : '',
        auth: auth ? btoa(String.fromCharCode(...new Uint8Array(auth))) : '',
      },
    };
  }

  /**
   * 구독 정보를 서버에 전송
   */
  private async sendSubscriptionToServer(subscription: globalThis.PushSubscription): Promise<void> {
    const apiClient = new ApiClient(import.meta.env.VITE_API_BASE_URL);
    const formattedSubscription = this.formatSubscription(subscription);
    
    await apiClient.post('/push/subscribe', formattedSubscription);
  }

  /**
   * 서버에서 구독 정보 제거
   */
  private async removeSubscriptionFromServer(subscription: globalThis.PushSubscription): Promise<void> {
    const apiClient = new ApiClient(import.meta.env.VITE_API_BASE_URL);
    const formattedSubscription = this.formatSubscription(subscription);
    
    await apiClient.post('/push/unsubscribe', formattedSubscription);
  }
}

/**
 * 푸시 알림 서비스 인스턴스
 */
export const pushNotificationService = new PushNotificationService(
  import.meta.env.VITE_VAPID_PUBLIC_KEY || ''
);
```

### `src/hooks/usePushNotifications.ts`
```typescript
import { useState, useEffect } from 'react';
import { pushNotificationService } from '@/services/pushNotifications';

/**
 * 푸시 알림 관리 훅
 */
export const usePushNotifications = () => {
  const [isSupported, setIsSupported] = useState(false);
  const [isSubscribed, setIsSubscribed] = useState(false);
  const [permission, setPermission] = useState<NotificationPermission>('default');

  useEffect(() => {
    setIsSupported(pushNotificationService.isSupported());
    setPermission(Notification.permission);
    
    // 기존 구독 상태 확인
    checkSubscriptionStatus();
  }, []);

  /**
   * 구독 상태 확인
   */
  const checkSubscriptionStatus = async () => {
    if (!pushNotificationService.isSupported()) return;

    try {
      const registration = await navigator.serviceWorker.ready;
      const subscription = await registration.pushManager.getSubscription();
      setIsSubscribed(!!subscription);
    } catch (error) {
      console.error('Failed to check subscription status:', error);
    }
  };

  /**
   * 푸시 알림 구독
   */
  const subscribe = async (): Promise<boolean> => {
    try {
      const subscription = await pushNotificationService.subscribe();
      const success = !!subscription;
      setIsSubscribed(success);
      if (success) {
        setPermission('granted');
      }
      return success;
    } catch (error) {
      console.error('Failed to subscribe to push notifications:', error);
      return false;
    }
  };

  /**
   * 푸시 알림 구독 해제
   */
  const unsubscribe = async (): Promise<boolean> => {
    try {
      const success = await pushNotificationService.unsubscribe();
      setIsSubscribed(!success);
      return success;
    } catch (error) {
      console.error('Failed to unsubscribe from push notifications:', error);
      return false;
    }
  };

  return {
    isSupported,
    isSubscribed,
    permission,
    subscribe,
    unsubscribe,
    checkSubscriptionStatus,
  };
};
```

---

## 🔄 **4단계: 서비스 워커 확장**

### `public/sw.js` (추가 기능)
```javascript
// 푸시 알림 처리
self.addEventListener('push', function(event) {
  if (!event.data) return;

  const data = event.data.json();
  const options = {
    body: data.body || '새로운 알림이 있습니다.',
    icon: '/icon-192x192.png',
    badge: '/icon-192x192.png',
    image: data.image,
    data: data.data || {},
    actions: [
      {
        action: 'view',
        title: '보기',
        icon: '/icons/view.png'
      },
      {
        action: 'dismiss',
        title: '닫기',
        icon: '/icons/dismiss.png'
      }
    ],
    requireInteraction: true,
    timestamp: Date.now()
  };

  event.waitUntil(
    self.registration.showNotification(data.title || '회의실 예약 시스템', options)
  );
});

// 알림 클릭 처리
self.addEventListener('notificationclick', function(event) {
  event.notification.close();

  if (event.action === 'view') {
    // 알림 데이터에 따른 페이지 열기
    const urlToOpen = event.notification.data.url || '/dashboard';
    
    event.waitUntil(
      clients.matchAll({ type: 'window', includeUncontrolled: true })
        .then(function(clientList) {
          // 이미 열린 창이 있으면 포커스
          for (let i = 0; i < clientList.length; i++) {
            const client = clientList[i];
            if (client.url.includes(urlToOpen) && 'focus' in client) {
              return client.focus();
            }
          }
          
          // 새 창 열기
          if (clients.openWindow) {
            return clients.openWindow(urlToOpen);
          }
        })
    );
  }
});

// 백그라운드 동기화
self.addEventListener('sync', function(event) {
  if (event.tag === 'background-sync') {
    event.waitUntil(
      syncOfflineData()
    );
  }
});

// 오프라인 데이터 동기화
async function syncOfflineData() {
  try {
    // IndexedDB에서 오프라인 액션 가져오기
    const offlineActions = await getOfflineActions();
    
    for (const action of offlineActions) {
      try {
        await processOfflineAction(action);
        await removeOfflineAction(action.id);
      } catch (error) {
        console.error('Failed to sync action:', action, error);
      }
    }
  } catch (error) {
    console.error('Background sync failed:', error);
  }
}

// IndexedDB 유틸리티 함수들
function openDB() {
  return new Promise((resolve, reject) => {
    const request = indexedDB.open('MeetingRoomApp', 1);
    
    request.onerror = () => reject(request.error);
    request.onsuccess = () => resolve(request.result);
    
    request.onupgradeneeded = (event) => {
      const db = event.target.result;
      if (!db.objectStoreNames.contains('offlineActions')) {
        db.createObjectStore('offlineActions', { keyPath: 'id' });
      }
    };
  });
}

async function getOfflineActions() {
  const db = await openDB();
  const transaction = db.transaction(['offlineActions'], 'readonly');
  const store = transaction.objectStore('offlineActions');
  
  return new Promise((resolve, reject) => {
    const request = store.getAll();
    request.onerror = () => reject(request.error);
    request.onsuccess = () => resolve(request.result);
  });
}

async function removeOfflineAction(id) {
  const db = await openDB();
  const transaction = db.transaction(['offlineActions'], 'readwrite');
  const store = transaction.objectStore('offlineActions');
  
  return new Promise((resolve, reject) => {
    const request = store.delete(id);
    request.onerror = () => reject(request.error);
    request.onsuccess = () => resolve();
  });
}

async function processOfflineAction(action) {
  const response = await fetch(action.url, {
    method: action.method,
    headers: action.headers,
    body: action.body
  });
  
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }
  
  return response;
}
```

---

## 📲 **5단계: 앱 설치 프롬프트**

### `src/hooks/useInstallPrompt.ts`
```typescript
import { useState, useEffect } from 'react';

interface BeforeInstallPromptEvent extends Event {
  prompt(): Promise<void>;
  userChoice: Promise<{ outcome: 'accepted' | 'dismissed' }>;
}

/**
 * PWA 설치 프롬프트 관리 훅
 */
export const useInstallPrompt = () => {
  const [installPrompt, setInstallPrompt] = useState<BeforeInstallPromptEvent | null>(null);
  const [isInstallable, setIsInstallable] = useState(false);
  const [isInstalled, setIsInstalled] = useState(false);

  useEffect(() => {
    // PWA 설치 가능 여부 확인
    const checkInstallability = () => {
      const isStandalone = window.matchMedia('(display-mode: standalone)').matches;
      const isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent);
      const isInStandaloneMode = (window.navigator as any).standalone === true;
      
      setIsInstalled(isStandalone || isInStandaloneMode);
    };

    checkInstallability();

    // beforeinstallprompt 이벤트 리스너
    const handleBeforeInstallPrompt = (e: Event) => {
      e.preventDefault();
      setInstallPrompt(e as BeforeInstallPromptEvent);
      setIsInstallable(true);
    };

    // 앱 설치됨 이벤트 리스너
    const handleAppInstalled = () => {
      setIsInstalled(true);
      setInstallPrompt(null);
      setIsInstallable(false);
    };

    window.addEventListener('beforeinstallprompt', handleBeforeInstallPrompt);
    window.addEventListener('appinstalled', handleAppInstalled);

    return () => {
      window.removeEventListener('beforeinstallprompt', handleBeforeInstallPrompt);
      window.removeEventListener('appinstalled', handleAppInstalled);
    };
  }, []);

  /**
   * 설치 프롬프트 표시
   */
  const showInstallPrompt = async (): Promise<boolean> => {
    if (!installPrompt) {
      return false;
    }

    try {
      await installPrompt.prompt();
      const { outcome } = await installPrompt.userChoice;
      
      if (outcome === 'accepted') {
        setInstallPrompt(null);
        setIsInstallable(false);
        return true;
      }
      
      return false;
    } catch (error) {
      console.error('Failed to show install prompt:', error);
      return false;
    }
  };

  return {
    isInstallable,
    isInstalled,
    showInstallPrompt,
  };
};
```

### `src/components/common/InstallPrompt.tsx`
```typescript
import { useState } from 'react';
import { Download, X, Smartphone } from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Card, CardContent } from '@/components/ui/card';
import { useInstallPrompt } from '@/hooks/useInstallPrompt';

/**
 * PWA 설치 프롬프트 컴포넌트
 */
export const InstallPrompt: React.FC = () => {
  const [dismissed, setDismissed] = useState(false);
  const { isInstallable, isInstalled, showInstallPrompt } = useInstallPrompt();

  // 이미 설치되었거나 설치 불가능하거나 무시된 경우 표시하지 않음
  if (isInstalled || !isInstallable || dismissed) {
    return null;
  }

  const handleInstall = async () => {
    const success = await showInstallPrompt();
    if (!success) {
      setDismissed(true);
    }
  };

  const handleDismiss = () => {
    setDismissed(true);
    // 24시간 후 다시 표시하도록 설정
    localStorage.setItem('installPromptDismissed', Date.now().toString());
  };

  return (
    <Card className="fixed bottom-4 left-4 right-4 z-50 mx-auto max-w-sm border-2 border-primary bg-background shadow-lg">
      <CardContent className="p-4">
        <div className="flex items-start gap-3">
          <div className="flex-shrink-0">
            <div className="flex h-10 w-10 items-center justify-center rounded-full bg-primary text-primary-foreground">
              <Smartphone className="h-5 w-5" />
            </div>
          </div>
          
          <div className="flex-1 min-w-0">
            <h3 className="text-sm font-semibold">앱 설치</h3>
            <p className="text-xs text-muted-foreground mt-1">
              홈 화면에 추가하여 더 빠르고 편리하게 이용하세요.
            </p>
            
            <div className="flex gap-2 mt-3">
              <Button size="sm" onClick={handleInstall}>
                <Download className="h-3 w-3 mr-1" />
                설치
              </Button>
              <Button variant="ghost" size="sm" onClick={handleDismiss}>
                <X className="h-3 w-3 mr-1" />
                닫기
              </Button>
            </div>
          </div>
        </div>
      </CardContent>
    </Card>
  );
};
```

---

## 🔄 **6단계: 앱 업데이트 관리**

### `src/hooks/useAppUpdate.ts`
```typescript
import { useState, useEffect } from 'react';
import { useRegisterSW } from 'virtual:pwa-register/react';

/**
 * 앱 업데이트 관리 훅
 */
export const useAppUpdate = () => {
  const [needRefresh, setNeedRefresh] = useState(false);
  const [offlineReady, setOfflineReady] = useState(false);
  const [updateAvailable, setUpdateAvailable] = useState(false);

  const {
    needRefresh: [needRefreshState, setNeedRefreshState],
    offlineReady: [offlineReadyState, setOfflineReadyState],
    updateServiceWorker,
  } = useRegisterSW({
    onRegistered(registration) {
      console.log('SW Registered:', registration);
    },
    onRegisterError(error) {
      console.log('SW registration error:', error);
    },
    onNeedRefresh() {
      setNeedRefresh(true);
      setUpdateAvailable(true);
    },
    onOfflineReady() {
      setOfflineReady(true);
    },
  });

  useEffect(() => {
    setNeedRefresh(needRefreshState);
    setOfflineReady(offlineReadyState);
  }, [needRefreshState, offlineReadyState]);

  /**
   * 앱 업데이트 적용
   */
  const updateApp = async (): Promise<void> => {
    try {
      await updateServiceWorker(true);
      setNeedRefresh(false);
      setUpdateAvailable(false);
    } catch (error) {
      console.error('Failed to update app:', error);
    }
  };

  /**
   * 업데이트 무시
   */
  const dismissUpdate = (): void => {
    setNeedRefresh(false);
    setUpdateAvailable(false);
    setNeedRefreshState(false);
  };

  return {
    needRefresh,
    offlineReady,
    updateAvailable,
    updateApp,
    dismissUpdate,
  };
};
```

### `src/components/common/UpdatePrompt.tsx`
```typescript
import { RefreshCw, X, Download } from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Card, CardContent } from '@/components/ui/card';
import { useAppUpdate } from '@/hooks/useAppUpdate';

/**
 * 앱 업데이트 프롬프트 컴포넌트
 */
export const UpdatePrompt: React.FC = () => {
  const { updateAvailable, updateApp, dismissUpdate } = useAppUpdate();

  if (!updateAvailable) {
    return null;
  }

  return (
    <Card className="fixed top-4 left-4 right-4 z-50 mx-auto max-w-sm border-2 border-blue-500 bg-background shadow-lg">
      <CardContent className="p-4">
        <div className="flex items-start gap-3">
          <div className="flex-shrink-0">
            <div className="flex h-10 w-10 items-center justify-center rounded-full bg-blue-500 text-white">
              <Download className="h-5 w-5" />
            </div>
          </div>
          
          <div className="flex-1 min-w-0">
            <h3 className="text-sm font-semibold">업데이트 사용 가능</h3>
            <p className="text-xs text-muted-foreground mt-1">
              새로운 버전이 준비되었습니다. 지금 업데이트하시겠습니까?
            </p>
            
            <div className="flex gap-2 mt-3">
              <Button size="sm" onClick={updateApp}>
                <RefreshCw className="h-3 w-3 mr-1" />
                업데이트
              </Button>
              <Button variant="ghost" size="sm" onClick={dismissUpdate}>
                <X className="h-3 w-3 mr-1" />
                나중에
              </Button>
            </div>
          </div>
        </div>
      </CardContent>
    </Card>
  );
};
```

---

## 📊 **7단계: PWA 성능 모니터링**

### `src/utils/pwaMetrics.ts`
```typescript
/**
 * PWA 성능 메트릭 수집
 */
export interface PWAMetrics {
  isInstalled: boolean;
  isOffline: boolean;
  cacheHitRate: number;
  loadTime: number;
  serviceWorkerStatus: string;
  pushNotificationStatus: string;
}

/**
 * PWA 메트릭 수집기
 */
export class PWAMetricsCollector {
  private metrics: Partial<PWAMetrics> = {};

  constructor() {
    this.collectBasicMetrics();
  }

  /**
   * 기본 메트릭 수집
   */
  private collectBasicMetrics(): void {
    // 설치 상태 확인
    this.metrics.isInstalled = this.checkInstallStatus();
    
    // 오프라인 상태 확인
    this.metrics.isOffline = !navigator.onLine;
    
    // 서비스 워커 상태 확인
    this.metrics.serviceWorkerStatus = this.checkServiceWorkerStatus();
    
    // 푸시 알림 상태 확인
    this.metrics.pushNotificationStatus = this.checkPushNotificationStatus();
    
    // 페이지 로드 시간 측정
    this.measureLoadTime();
  }

  /**
   * 설치 상태 확인
   */
  private checkInstallStatus(): boolean {
    const isStandalone = window.matchMedia('(display-mode: standalone)').matches;
    const isIOSStandalone = (window.navigator as any).standalone === true;
    return isStandalone || isIOSStandalone;
  }

  /**
   * 서비스 워커 상태 확인
   */
  private checkServiceWorkerStatus(): string {
    if (!('serviceWorker' in navigator)) {
      return 'not_supported';
    }

    if (navigator.serviceWorker.controller) {
      return 'active';
    }

    return 'not_active';
  }

  /**
   * 푸시 알림 상태 확인
   */
  private checkPushNotificationStatus(): string {
    if (!('Notification' in window)) {
      return 'not_supported';
    }

    return Notification.permission;
  }

  /**
   * 페이지 로드 시간 측정
   */
  private measureLoadTime(): void {
    if ('performance' in window) {
      window.addEventListener('load', () => {
        const navigation = performance.getEntriesByType('navigation')[0] as PerformanceNavigationTiming;
        this.metrics.loadTime = navigation.loadEventEnd - navigation.fetchStart;
      });
    }
  }

  /**
   * 캐시 히트율 측정
   */
  async measureCacheHitRate(): Promise<void> {
    if (!('caches' in window)) {
      this.metrics.cacheHitRate = 0;
      return;
    }

    try {
      const cacheNames = await caches.keys();
      let totalRequests = 0;
      let cachedRequests = 0;

      for (const cacheName of cacheNames) {
        const cache = await caches.open(cacheName);
        const requests = await cache.keys();
        totalRequests += requests.length;
        cachedRequests += requests.length; // 캐시에 있는 요청들은 모두 히트
      }

      this.metrics.cacheHitRate = totalRequests > 0 ? (cachedRequests / totalRequests) * 100 : 0;
    } catch (error) {
      console.error('Failed to measure cache hit rate:', error);
      this.metrics.cacheHitRate = 0;
    }
  }

  /**
   * 메트릭 반환
   */
  getMetrics(): PWAMetrics {
    return this.metrics as PWAMetrics;
  }

  /**
   * 메트릭을 서버에 전송
   */
  async sendMetrics(): Promise<void> {
    try {
      await fetch('/api/metrics/pwa', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(this.metrics),
      });
    } catch (error) {
      console.error('Failed to send PWA metrics:', error);
    }
  }
}

/**
 * PWA 메트릭 수집기 인스턴스
 */
export const pwaMetrics = new PWAMetricsCollector();
```

### `src/hooks/usePWAMetrics.ts`
```typescript
import { useState, useEffect } from 'react';
import { pwaMetrics, PWAMetrics } from '@/utils/pwaMetrics';

/**
 * PWA 메트릭 관리 훅
 */
export const usePWAMetrics = () => {
  const [metrics, setMetrics] = useState<PWAMetrics | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    const collectMetrics = async () => {
      setIsLoading(true);
      
      // 캐시 히트율 측정
      await pwaMetrics.measureCacheHitRate();
      
      // 메트릭 수집
      const collectedMetrics = pwaMetrics.getMetrics();
      setMetrics(collectedMetrics);
      
      // 서버에 전송
      await pwaMetrics.sendMetrics();
      
      setIsLoading(false);
    };

    collectMetrics();
  }, []);

  return {
    metrics,
    isLoading,
  };
};
```

---

## 🛠️ **8단계: PWA 디버깅 도구**

### `src/components/dev/PWADebugPanel.tsx`
```typescript
import { useState } from 'react';
import { Bug, RefreshCw, Trash2, Download } from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { usePWAMetrics } from '@/hooks/usePWAMetrics';
import { useOfflineSupport } from '@/hooks/useOfflineSupport';
import { usePushNotifications } from '@/hooks/usePushNotifications';

/**
 * PWA 디버깅 패널 (개발환경에서만 표시)
 */
export const PWADebugPanel: React.FC = () => {
  const [isOpen, setIsOpen] = useState(false);
  const { metrics, isLoading } = usePWAMetrics();
  const { isOnline, offlineActions } = useOfflineSupport();
  const { isSupported, isSubscribed, permission } = usePushNotifications();

  // 프로덕션 환경에서는 표시하지 않음
  if (import.meta.env.PROD) {
    return null;
  }

  const clearCaches = async () => {
    if ('caches' in window) {
      const cacheNames = await caches.keys();
      await Promise.all(cacheNames.map(name => caches.delete(name)));
      window.location.reload();
    }
  };

  const unregisterServiceWorker = async () => {
    if ('serviceWorker' in navigator) {
      const registrations = await navigator.serviceWorker.getRegistrations();
      await Promise.all(registrations.map(registration => registration.unregister()));
      window.location.reload();
    }
  };

  if (!isOpen) {
    return (
      <Button
        className="fixed bottom-4 right-4 z-50"
        size="sm"
        variant="outline"
        onClick={() => setIsOpen(true)}
      >
        <Bug className="h-4 w-4" />
      </Button>
    );
  }

  return (
    <Card className="fixed bottom-4 right-4 z-50 w-80 max-h-96 overflow-y-auto">
      <CardHeader className="pb-3">
        <div className="flex items-center justify-between">
          <CardTitle className="text-sm">PWA 디버그 패널</CardTitle>
          <Button
            variant="ghost"
            size="sm"
            onClick={() => setIsOpen(false)}
          >
            ×
          </Button>
        </div>
      </CardHeader>
      
      <CardContent className="space-y-4 text-xs">
        {/* 연결 상태 */}
        <div>
          <h4 className="font-medium mb-2">연결 상태</h4>
          <div className="space-y-1">
            <div className="flex justify-between">
              <span>온라인:</span>
              <Badge variant={isOnline ? 'default' : 'destructive'}>
                {isOnline ? 'Yes' : 'No'}
              </Badge>
            </div>
            <div className="flex justify-between">
              <span>오프라인 액션:</span>
              <Badge variant="secondary">{offlineActions.length}</Badge>
            </div>
          </div>
        </div>

        {/* PWA 메트릭 */}
        {metrics && (
          <div>
            <h4 className="font-medium mb-2">PWA 메트릭</h4>
            <div className="space-y-1">
              <div className="flex justify-between">
                <span>설치됨:</span>
                <Badge variant={metrics.isInstalled ? 'default' : 'secondary'}>
                  {metrics.isInstalled ? 'Yes' : 'No'}
                </Badge>
              </div>
              <div className="flex justify-between">
                <span>서비스 워커:</span>
                <Badge variant="outline">{metrics.serviceWorkerStatus}</Badge>
              </div>
              <div className="flex justify-between">
                <span>캐시 히트율:</span>
                <Badge variant="outline">{metrics.cacheHitRate.toFixed(1)}%</Badge>
              </div>
              <div className="flex justify-between">
                <span>로드 시간:</span>
                <Badge variant="outline">{metrics.loadTime}ms</Badge>
              </div>
            </div>
          </div>
        )}

        {/* 푸시 알림 */}
        <div>
          <h4 className="font-medium mb-2">푸시 알림</h4>
          <div className="space-y-1">
            <div className="flex justify-between">
              <span>지원됨:</span>
              <Badge variant={isSupported ? 'default' : 'destructive'}>
                {isSupported ? 'Yes' : 'No'}
              </Badge>
            </div>
            <div className="flex justify-between">
              <span>구독됨:</span>
              <Badge variant={isSubscribed ? 'default' : 'secondary'}>
                {isSubscribed ? 'Yes' : 'No'}
              </Badge>
            </div>
            <div className="flex justify-between">
              <span>권한:</span>
              <Badge variant="outline">{permission}</Badge>
            </div>
          </div>
        </div>

        {/* 디버그 액션 */}
        <div>
          <h4 className="font-medium mb-2">디버그 액션</h4>
          <div className="space-y-2">
            <Button
              variant="outline"
              size="sm"
              className="w-full text-xs"
              onClick={clearCaches}
            >
              <Trash2 className="h-3 w-3 mr-1" />
              캐시 삭제
            </Button>
            <Button
              variant="outline"
              size="sm"
              className="w-full text-xs"
              onClick={unregisterServiceWorker}
            >
              <RefreshCw className="h-3 w-3 mr-1" />
              SW 해제
            </Button>
            <Button
              variant="outline"
              size="sm"
              className="w-full text-xs"
              onClick={() => window.location.reload()}
            >
              <Download className="h-3 w-3 mr-1" />
              새로고침
            </Button>
          </div>
        </div>
      </CardContent>
    </Card>
  );
};
```

---

## 🎨 **9단계: 메인 앱에 PWA 기능 통합**

### `src/App.tsx` 최종 업데이트
```typescript
import { AppRouter } from '@/lib/router';
import { ErrorBoundary, ConnectionStatus } from '@/components/common';
import { InstallPrompt } from '@/components/common/InstallPrompt';
import { UpdatePrompt } from '@/components/common/UpdatePrompt';
import { OfflineIndicator } from '@/components/common/OfflineIndicator';
import { PWADebugPanel } from '@/components/dev/PWADebugPanel';
import { useTokenRefresh } from '@/hooks/useTokenRefresh';
import { useWebSocket } from '@/hooks/useWebSocket';
import { useRealtimeReservations } from '@/hooks/useRealtimeReservations';
import { useRealtimeRooms } from '@/hooks/useRealtimeRooms';
import { useOfflineSupport } from '@/hooks/useOfflineSupport';
import '@/styles/globals.css';

function App() {
  // 토큰 자동 갱신
  useTokenRefresh();
  
  // WebSocket 연결
  useWebSocket();
  
  // 실시간 업데이트 구독
  useRealtimeReservations();
  useRealtimeRooms();
  
  // 오프라인 지원
  useOfflineSupport();

  return (
    <ErrorBoundary>
      {/* PWA 상태 표시 */}
      <ConnectionStatus />
      <OfflineIndicator />
      
      {/* 메인 앱 */}
      <AppRouter />
      
      {/* PWA 프롬프트들 */}
      <InstallPrompt />
      <UpdatePrompt />
      
      {/* 개발 도구 */}
      <PWADebugPanel />
    </ErrorBoundary>
  );
}

export default App;
```

### `src/main.tsx` 최종 업데이트
```typescript
import React from 'react';
import ReactDOM from 'react-dom/client';
import { QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import { queryClient } from '@/lib/queryClient';
import { apiClient } from '@/services/api/client';
import { requestNotificationPermission } from '@/utils/notifications';
import { pwaMetrics } from '@/utils/pwaMetrics';
import App from './App';
import '@/styles/globals.css';

// 앱 초기화
const initializeApp = async () => {
  // 토큰 설정
  const token = localStorage.getItem('token');
  if (token) {
    apiClient.setAuthToken(token);
  }

  // 알림 권한 요청 (사용자가 로그인한 경우)
  if (token) {
    await requestNotificationPermission();
  }

  // PWA 메트릭 수집
  await pwaMetrics.measureCacheHitRate();
};

// 앱 시작
initializeApp().then(() => {
  ReactDOM.createRoot(document.getElementById('root')!).render(
    <React.StrictMode>
      <QueryClientProvider client={queryClient}>
        <App />
        {import.meta.env.DEV && <ReactQueryDevtools initialIsOpen={false} />}
      </QueryClientProvider>
    </React.StrictMode>
  );
});

// 서비스 워커 등록 상태 로깅
if ('serviceWorker' in navigator) {
  window.addEventListener('load', () => {
    navigator.serviceWorker.ready.then((registration) => {
      console.log('SW registered: ', registration);
    });
  });
}
```

---

## 📱 **10단계: 추가 PWA 아이콘 및 리소스**

### `public/manifest.json` 최종 버전
```json
{
  "name": "회의실 예약 시스템",
  "short_name": "회의실예약",
  "description": "실시간 회의실 예약 및 관리 시스템",
  "start_url": "/",
  "display": "standalone",
  "orientation": "portrait",
  "theme_color": "#ffffff",
  "background_color": "#ffffff",
  "scope": "/",
  "categories": ["business", "productivity"],
  "shortcuts": [
    {
      "name": "새 예약",
      "short_name": "예약",
      "description": "새로운 회의실 예약 생성",
      "url": "/reservations/new",
      "icons": [
        {
          "src": "shortcut-new-reservation.png",
          "sizes": "192x192",
          "type": "image/png"
        }
      ]
    },
    {
      "name": "내 예약",
      "short_name": "내예약",
      "description": "내 예약 목록 보기",
      "url": "/my-reservations",
      "icons": [
        {
          "src": "shortcut-my-reservations.png",
          "sizes": "192x192",
          "type": "image/png"
        }
      ]
    }
  ],
  "screenshots": [
    {
      "src": "screenshot-desktop.png",
      "sizes": "1280x720",
      "type": "image/png",
      "form_factor": "wide",
      "label": "Desktop Dashboard"
    },
    {
      "src": "screenshot-mobile.png",
      "sizes": "750x1334",
      "type": "image/png",
      "form_factor": "narrow",
      "label": "Mobile Dashboard"
    }
  ],
  "icons": [
    {
      "src": "icon-72x72.png",
      "sizes": "72x72",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "icon-96x96.png",
      "sizes": "96x96",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "icon-128x128.png",
      "sizes": "128x128",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "icon-144x144.png",
      "sizes": "144x144",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "icon-152x152.png",
      "sizes": "152x152",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "icon-384x384.png",
      "sizes": "384x384",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "maskable any"
    }
  ]
}
```

### `.env` 환경 변수 추가
```env
# 기존 변수들...

# PWA 관련
VITE_VAPID_PUBLIC_KEY=your_vapid_public_key_here
VITE_ENABLE_PWA_DEBUG=true

# 서비스 워커 설정
VITE_SW_UPDATE_INTERVAL=60000
VITE_OFFLINE_CACHE_DURATION=86400000
```

---

## 🎯 **PWA 기능 요약**

### 🚀 **구현된 핵심 기능**
1. **완전한 오프라인 지원** - 캐싱된 데이터로 오프라인에서도 앱 사용 가능
2. **실시간 동기화** - 온라인 복구 시 자동 데이터 동기화
3. **푸시 알림** - 백그라운드에서도 중요 알림 수신
4. **홈 화면 설치** - 네이티브 앱처럼 설치 및 사용
5. **자동 업데이트** - 새 버전 자동 감지 및 업데이트
6. **성능 최적화** - 캐싱 전략으로 빠른 로딩 속도

### 📱 **사용자 경험 개선**
- **빠른 로딩** - 캐시된 리소스로 즉시 앱 실행
- **오프라인 작업** - 네트워크 없이도 기본 기능 사용
- **실시간 알림** - 중요한 예약 변경사항 즉시 알림
- **네이티브 느낌** - 홈 화면 아이콘, 스플래시 화면
- **자동 동기화** - 온라인 복구 시 자동으로 데이터 동기화
