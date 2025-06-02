# 4️⃣ API 연동 가이드

## 🎯 **목표**
TanStack Query와 Fetch API를 사용하여 백엔드와의 데이터 통신을 설정하고, 효율적인 서버 상태 관리를 구현합니다.

---

## 📋 **API 구조 설계**

### 🌐 **API 엔드포인트 구조**
```
/api/auth
├── POST /login         → 로그인
├── POST /logout        → 로그아웃
├── POST /refresh       → 토큰 갱신
└── GET  /me           → 현재 사용자 정보

/api/rooms
├── GET    /rooms       → 회의실 목록
├── POST   /rooms       → 회의실 생성
├── GET    /rooms/:id   → 회의실 상세
├── PUT    /rooms/:id   → 회의실 수정
└── DELETE /rooms/:id   → 회의실 삭제

/api/reservations
├── GET    /reservations     → 예약 목록
├── POST   /reservations     → 예약 생성
├── GET    /reservations/:id → 예약 상세
├── PUT    /reservations/:id → 예약 수정
└── DELETE /reservations/:id → 예약 삭제

/api/users
├── GET    /users/me/reservations → 내 예약 목록
├── PUT    /users/me            → 프로필 수정
└── GET    /users/me/stats      → 사용자 통계
```

---

## 🔧 **1단계: API 클라이언트 설정**

### `src/services/api/client.ts`
```typescript
import { API_BASE_URL } from '@/utils/constants';

/**
 * API 응답 타입 정의
 */
export interface ApiResponse<T = any> {
  success: boolean;
  data: T;
  message?: string;
  errors?: Record<string, string[]>;
}

/**
 * API 에러 클래스
 */
export class ApiError extends Error {
  constructor(
    public status: number,
    public message: string,
    public errors?: Record<string, string[]>
  ) {
    super(message);
    this.name = 'ApiError';
  }
}

/**
 * HTTP 메서드 타입
 */
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH';

/**
 * 요청 옵션 인터페이스
 */
interface RequestOptions {
  method?: HttpMethod;
  headers?: Record<string, string>;
  body?: any;
  params?: Record<string, string | number>;
}

/**
 * 기본 HTTP 클라이언트 클래스
 */
export class ApiClient {
  private baseURL: string;
  private defaultHeaders: Record<string, string>;

  constructor(baseURL: string) {
    this.baseURL = baseURL;
    this.defaultHeaders = {
      'Content-Type': 'application/json',
    };
  }

  /**
   * 인증 토큰 설정
   */
  setAuthToken(token: string): void {
    this.defaultHeaders.Authorization = `Bearer ${token}`;
  }

  /**
   * 인증 토큰 제거
   */
  removeAuthToken(): void {
    delete this.defaultHeaders.Authorization;
  }

  /**
   * URL 파라미터 생성
   */
  private buildURL(endpoint: string, params?: Record<string, string | number>): string {
    const url = new URL(endpoint, this.baseURL);
    
    if (params) {
      Object.entries(params).forEach(([key, value]) => {
        url.searchParams.append(key, String(value));
      });
    }
    
    return url.toString();
  }

  /**
   * HTTP 요청 실행
   */
  private async request<T>(
    endpoint: string,
    options: RequestOptions = {}
  ): Promise<ApiResponse<T>> {
    const {
      method = 'GET',
      headers = {},
      body,
      params,
    } = options;

    const url = this.buildURL(endpoint, params);
    const config: RequestInit = {
      method,
      headers: {
        ...this.defaultHeaders,
        ...headers,
      },
    };

    // Body 처리
    if (body) {
      if (body instanceof FormData) {
        // FormData인 경우 Content-Type 헤더 제거 (브라우저가 자동 설정)
        delete config.headers!['Content-Type'];
        config.body = body;
      } else {
        config.body = JSON.stringify(body);
      }
    }

    try {
      const response = await fetch(url, config);
      
      // 응답 데이터 파싱
      let data: ApiResponse<T>;
      const contentType = response.headers.get('content-type');
      
      if (contentType && contentType.includes('application/json')) {
        data = await response.json();
      } else {
        // JSON이 아닌 경우 텍스트로 처리
        const text = await response.text();
        data = {
          success: response.ok,
          data: text as T,
        };
      }

      // 에러 응답 처리
      if (!response.ok) {
        throw new ApiError(
          response.status,
          data.message || `HTTP ${response.status}: ${response.statusText}`,
          data.errors
        );
      }

      return data;
    } catch (error) {
      // 네트워크 에러 등 처리
      if (error instanceof ApiError) {
        throw error;
      }
      
      throw new ApiError(
        0,
        error instanceof Error ? error.message : '알 수 없는 에러가 발생했습니다.'
      );
    }
  }

  /**
   * GET 요청
   */
  async get<T>(endpoint: string, params?: Record<string, string | number>): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'GET', params });
  }

  /**
   * POST 요청
   */
  async post<T>(endpoint: string, body?: any): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'POST', body });
  }

  /**
   * PUT 요청
   */
  async put<T>(endpoint: string, body?: any): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'PUT', body });
  }

  /**
   * DELETE 요청
   */
  async delete<T>(endpoint: string): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'DELETE' });
  }

  /**
   * PATCH 요청
   */
  async patch<T>(endpoint: string, body?: any): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'PATCH', body });
  }
}

/**
 * 기본 API 클라이언트 인스턴스
 */
export const apiClient = new ApiClient(API_BASE_URL);
```

### `src/utils/constants.ts` 업데이트
```typescript
// 기존 상수에 추가
export const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:3001/api';
export const WS_URL = import.meta.env.VITE_WS_URL || 'ws://localhost:3001';

/**
 * API 엔드포인트 상수
 */
export const API_ENDPOINTS = {
  // 인증
  AUTH: {
    LOGIN: '/auth/login',
    LOGOUT: '/auth/logout',
    REFRESH: '/auth/refresh',
    ME: '/auth/me',
  },
  
  // 회의실
  ROOMS: {
    LIST: '/rooms',
    CREATE: '/rooms',
    DETAIL: (id: string) => `/rooms/${id}`,
    UPDATE: (id: string) => `/rooms/${id}`,
    DELETE: (id: string) => `/rooms/${id}`,
  },
  
  // 예약
  RESERVATIONS: {
    LIST: '/reservations',
    CREATE: '/reservations',
    DETAIL: (id: string) => `/reservations/${id}`,
    UPDATE: (id: string) => `/reservations/${id}`,
    DELETE: (id: string) => `/reservations/${id}`,
  },
  
  // 사용자
  USERS: {
    MY_RESERVATIONS: '/users/me/reservations',
    UPDATE_PROFILE: '/users/me',
    STATS: '/users/me/stats',
  },
} as const;
```

---

## 📝 **2단계: TypeScript 타입 정의**

### `src/types/api.ts`
```typescript
/**
 * 페이지네이션 매개변수
 */
export interface PaginationParams {
  page?: number;
  limit?: number;
  sort?: string;
  order?: 'asc' | 'desc';
}

/**
 * 페이지네이션 응답
 */
export interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  limit: number;
  totalPages: number;
}

/**
 * 필터 매개변수
 */
export interface FilterParams {
  search?: string;
  startDate?: string;
  endDate?: string;
  status?: string;
  roomId?: string;
}
```

### `src/types/auth.ts`
```typescript
/**
 * 로그인 요청 데이터
 */
export interface LoginRequest {
  email: string;
  password: string;
}

/**
 * 로그인 응답 데이터
 */
export interface LoginResponse {
  user: User;
  token: string;
  refreshToken: string;
}

/**
 * 사용자 정보
 */
export interface User {
  id: string;
  email: string;
  name: string;
  role: 'admin' | 'user';
  avatar?: string;
  createdAt: string;
  updatedAt: string;
}
```

### `src/types/room.ts`
```typescript
/**
 * 회의실 정보
 */
export interface Room {
  id: string;
  name: string;
  description?: string;
  capacity: number;
  location: string;
  amenities: string[];
  isActive: boolean;
  createdAt: string;
  updatedAt: string;
}

/**
 * 회의실 생성/수정 요청 데이터
 */
export interface RoomFormData {
  name: string;
  description?: string;
  capacity: number;
  location: string;
  amenities: string[];
  isActive?: boolean;
}
```

### `src/types/reservation.ts`
```typescript
import { Room } from './room';
import { User } from './auth';

/**
 * 예약 상태
 */
export type ReservationStatus = 'pending' | 'confirmed' | 'cancelled' | 'completed';

/**
 * 예약 정보
 */
export interface Reservation {
  id: string;
  title: string;
  description?: string;
  startTime: string;
  endTime: string;
  status: ReservationStatus;
  room: Room;
  user: User;
  attendees?: string[];
  createdAt: string;
  updatedAt: string;
}

/**
 * 예약 생성/수정 요청 데이터
 */
export interface ReservationFormData {
  title: string;
  description?: string;
  roomId: string;
  startTime: string;
  endTime: string;
  attendees?: string[];
}
```

---

## 🔌 **3단계: API 서비스 구현**

### `src/services/api/auth.ts`
```typescript
import { apiClient, ApiResponse } from './client';
import { API_ENDPOINTS } from '@/utils/constants';
import { LoginRequest, LoginResponse, User } from '@/types/auth';

/**
 * 인증 관련 API 서비스
 */
export const authAPI = {
  /**
   * 로그인
   */
  login: async (credentials: LoginRequest): Promise<ApiResponse<LoginResponse>> => {
    return apiClient.post<LoginResponse>(API_ENDPOINTS.AUTH.LOGIN, credentials);
  },

  /**
   * 로그아웃
   */
  logout: async (): Promise<ApiResponse<void>> => {
    return apiClient.post<void>(API_ENDPOINTS.AUTH.LOGOUT);
  },

  /**
   * 토큰 갱신
   */
  refreshToken: async (refreshToken: string): Promise<ApiResponse<{ token: string }>> => {
    return apiClient.post<{ token: string }>(API_ENDPOINTS.AUTH.REFRESH, {
      refreshToken,
    });
  },

  /**
   * 현재 사용자 정보 조회
   */
  getMe: async (): Promise<ApiResponse<User>> => {
    return apiClient.get<User>(API_ENDPOINTS.AUTH.ME);
  },
};
```

### `src/services/api/rooms.ts`
```typescript
import { apiClient, ApiResponse } from './client';
import { API_ENDPOINTS } from '@/utils/constants';
import { Room, RoomFormData } from '@/types/room';
import { PaginatedResponse, PaginationParams, FilterParams } from '@/types/api';

/**
 * 회의실 관련 API 서비스
 */
export const roomsAPI = {
  /**
   * 회의실 목록 조회
   */
  getRooms: async (
    params?: PaginationParams & FilterParams
  ): Promise<ApiResponse<PaginatedResponse<Room>>> => {
    return apiClient.get<PaginatedResponse<Room>>(API_ENDPOINTS.ROOMS.LIST, params);
  },

  /**
   * 회의실 상세 조회
   */
  getRoom: async (id: string): Promise<ApiResponse<Room>> => {
    return apiClient.get<Room>(API_ENDPOINTS.ROOMS.DETAIL(id));
  },

  /**
   * 회의실 생성
   */
  createRoom: async (data: RoomFormData): Promise<ApiResponse<Room>> => {
    return apiClient.post<Room>(API_ENDPOINTS.ROOMS.CREATE, data);
  },

  /**
   * 회의실 수정
   */
  updateRoom: async (id: string, data: Partial<RoomFormData>): Promise<ApiResponse<Room>> => {
    return apiClient.put<Room>(API_ENDPOINTS.ROOMS.UPDATE(id), data);
  },

  /**
   * 회의실 삭제
   */
  deleteRoom: async (id: string): Promise<ApiResponse<void>> => {
    return apiClient.delete<void>(API_ENDPOINTS.ROOMS.DELETE(id));
  },
};
```

### `src/services/api/reservations.ts`
```typescript
import { apiClient, ApiResponse } from './client';
import { API_ENDPOINTS } from '@/utils/constants';
import { Reservation, ReservationFormData } from '@/types/reservation';
import { PaginatedResponse, PaginationParams, FilterParams } from '@/types/api';

/**
 * 예약 관련 API 서비스
 */
export const reservationsAPI = {
  /**
   * 예약 목록 조회
   */
  getReservations: async (
    params?: PaginationParams & FilterParams
  ): Promise<ApiResponse<PaginatedResponse<Reservation>>> => {
    return apiClient.get<PaginatedResponse<Reservation>>(API_ENDPOINTS.RESERVATIONS.LIST, params);
  },

  /**
   * 예약 상세 조회
   */
  getReservation: async (id: string): Promise<ApiResponse<Reservation>> => {
    return apiClient.get<Reservation>(API_ENDPOINTS.RESERVATIONS.DETAIL(id));
  },

  /**
   * 예약 생성
   */
  createReservation: async (data: ReservationFormData): Promise<ApiResponse<Reservation>> => {
    return apiClient.post<Reservation>(API_ENDPOINTS.RESERVATIONS.CREATE, data);
  },

  /**
   * 예약 수정
   */
  updateReservation: async (
    id: string,
    data: Partial<ReservationFormData>
  ): Promise<ApiResponse<Reservation>> => {
    return apiClient.put<Reservation>(API_ENDPOINTS.RESERVATIONS.UPDATE(id), data);
  },

  /**
   * 예약 취소
   */
  cancelReservation: async (id: string): Promise<ApiResponse<Reservation>> => {
    return apiClient.patch<Reservation>(API_ENDPOINTS.RESERVATIONS.UPDATE(id), {
      status: 'cancelled',
    });
  },

  /**
   * 예약 삭제
   */
  deleteReservation: async (id: string): Promise<ApiResponse<void>> => {
    return apiClient.delete<void>(API_ENDPOINTS.RESERVATIONS.DELETE(id));
  },
};
```

### `src/services/api/users.ts`
```typescript
import { apiClient, ApiResponse } from './client';
import { API_ENDPOINTS } from '@/utils/constants';
import { User } from '@/types/auth';
import { Reservation } from '@/types/reservation';
import { PaginatedResponse, PaginationParams } from '@/types/api';

/**
 * 사용자 통계 정보
 */
export interface UserStats {
  totalReservations: number;
  upcomingReservations: number;
  completedReservations: number;
  cancelledReservations: number;
}

/**
 * 프로필 업데이트 데이터
 */
export interface UpdateProfileData {
  name?: string;
  avatar?: File;
}

/**
 * 사용자 관련 API 서비스
 */
export const usersAPI = {
  /**
   * 내 예약 목록 조회
   */
  getMyReservations: async (
    params?: PaginationParams
  ): Promise<ApiResponse<PaginatedResponse<Reservation>>> => {
    return apiClient.get<PaginatedResponse<Reservation>>(
      API_ENDPOINTS.USERS.MY_RESERVATIONS,
      params
    );
  },

  /**
   * 프로필 업데이트
   */
  updateProfile: async (data: UpdateProfileData): Promise<ApiResponse<User>> => {
    // 파일 업로드가 있는 경우 FormData 사용
    if (data.avatar) {
      const formData = new FormData();
      if (data.name) formData.append('name', data.name);
      formData.append('avatar', data.avatar);
      return apiClient.put<User>(API_ENDPOINTS.USERS.UPDATE_PROFILE, formData);
    }

    return apiClient.put<User>(API_ENDPOINTS.USERS.UPDATE_PROFILE, data);
  },

  /**
   * 사용자 통계 조회
   */
  getStats: async (): Promise<ApiResponse<UserStats>> => {
    return apiClient.get<UserStats>(API_ENDPOINTS.USERS.STATS);
  },
};
```

---

## ⚡ **4단계: TanStack Query 설정**

### `src/lib/queryClient.ts`
```typescript
import { QueryClient, DefaultOptions } from '@tanstack/react-query';
import { ApiError } from '@/services/api/client';

/**
 * 기본 쿼리 옵션
 */
const queryConfig: DefaultOptions = {
  queries: {
    // 에러 재시도 설정
    retry: (failureCount, error) => {
      // 인증 에러(401, 403)는 재시도하지 않음
      if (error instanceof ApiError && [401, 403].includes(error.status)) {
        return false;
      }
      // 클라이언트 에러(4xx)는 재시도하지 않음
      if (error instanceof ApiError && error.status >= 400 && error.status < 500) {
        return false;
      }
      // 최대 3번까지 재시도
      return failureCount < 3;
    },
    
    // 백그라운드에서 자동 재검증 (5분)
    staleTime: 5 * 60 * 1000,
    
    // 캐시 유지 시간 (10분)
    gcTime: 10 * 60 * 1000,
    
    // 윈도우 포커스 시 재검증
    refetchOnWindowFocus: false,
    
    // 네트워크 재연결 시 재검증
    refetchOnReconnect: true,
  },
  
  mutations: {
    // 뮤테이션 에러 재시도 (1번만)
    retry: 1,
  },
};

/**
 * QueryClient 인스턴스 생성
 */
export const queryClient = new QueryClient({
  defaultOptions: queryConfig,
});

/**
 * 쿼리 키 팩토리
 */
export const queryKeys = {
  // 인증
  auth: {
    me: ['auth', 'me'] as const,
  },
  
  // 회의실
  rooms: {
    all: ['rooms'] as const,
    lists: () => [...queryKeys.rooms.all, 'list'] as const,
    list: (params: Record<string, any>) => [...queryKeys.rooms.lists(), params] as const,
    details: () => [...queryKeys.rooms.all, 'detail'] as const,
    detail: (id: string) => [...queryKeys.rooms.details(), id] as const,
  },
  
  // 예약
  reservations: {
    all: ['reservations'] as const,
    lists: () => [...queryKeys.reservations.all, 'list'] as const,
    list: (params: Record<string, any>) => [...queryKeys.reservations.lists(), params] as const,
    details: () => [...queryKeys.reservations.all, 'detail'] as const,
    detail: (id: string) => [...queryKeys.reservations.details(), id] as const,
    my: (params: Record<string, any>) => ['reservations', 'my', params] as const,
  },
  
  // 사용자
  users: {
    stats: ['users', 'stats'] as const,
  },
} as const;
```

---

## 🎣 **5단계: 커스텀 훅 구현**

### `src/hooks/useAuth.ts`
```typescript
import { useMutation, useQuery, useQueryClient } from '@tanstack/react-query';
import { authAPI } from '@/services/api/auth';
import { apiClient } from '@/services/api/client';
import { queryKeys } from '@/lib/queryClient';
import { LoginRequest } from '@/types/auth';
import { useNavigate } from 'react-router-dom';

/**
 * 인증 관련 훅
 */
export const useAuth = () => {
  const queryClient = useQueryClient();
  const navigate = useNavigate();

  // 현재 사용자 정보 조회
  const { data: user, isLoading, error } = useQuery({
    queryKey: queryKeys.auth.me,
    queryFn: async () => {
      const response = await authAPI.getMe();
      return response.data;
    },
    enabled: !!localStorage.getItem('token'), // 토큰이 있을 때만 실행
  });

  // 로그인 뮤테이션
  const loginMutation = useMutation({
    mutationFn: async (credentials: LoginRequest) => {
      const response = await authAPI.login(credentials);
      return response.data;
    },
    onSuccess: (data) => {
      // 토큰 저장 및 API 클라이언트에 설정
      localStorage.setItem('token', data.token);
      localStorage.setItem('refreshToken', data.refreshToken);
      apiClient.setAuthToken(data.token);
      
      // 사용자 정보 캐시 업데이트
      queryClient.setQueryData(queryKeys.auth.me, data.user);
      
      // 대시보드로 이동
      navigate('/dashboard');
    },
  });

  // 로그아웃 뮤테이션
  const logoutMutation = useMutation({
    mutationFn: authAPI.logout,
    onSuccess: () => {
      // 토큰 제거
      localStorage.removeItem('token');
      localStorage.removeItem('refreshToken');
      apiClient.removeAuthToken();
      
      // 모든 쿼리 캐시 초기화
      queryClient.clear();
      
      // 로그인 페이지로 이동
      navigate('/login');
    },
  });

  return {
    // 상태
    user,
    isLoading,
    error,
    isAuthenticated: !!user,
    
    // 액션
    login: loginMutation.mutate,
    logout: logoutMutation.mutate,
    
    // 로딩 상태
    isLoggingIn: loginMutation.isPending,
    isLoggingOut: logoutMutation.isPending,
    
    // 에러
    loginError: loginMutation.error,
    logoutError: logoutMutation.error,
  };
};
```

### `src/hooks/useRooms.ts`
```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { roomsAPI } from '@/services/api/rooms';
import { queryKeys } from '@/lib/queryClient';
import { RoomFormData } from '@/types/room';
import { PaginationParams, FilterParams } from '@/types/api';

/**
 * 회의실 관련 훅
 */
export const useRooms = (params?: PaginationParams & FilterParams) => {
  const queryClient = useQueryClient();

  // 회의실 목록 조회
  const {
    data: roomsData,
    isLoading,
    error,
    refetch,
  } = useQuery({
    queryKey: queryKeys.rooms.list(params || {}),
    queryFn: async () => {
      const response = await roomsAPI.getRooms(params);
      return response.data;
    },
  });

  // 회의실 생성 뮤테이션
  const createRoomMutation = useMutation({
    mutationFn: async (data: RoomFormData) => {
      const response = await roomsAPI.createRoom(data);
      return response.data;
    },
    onSuccess: () => {
      // 회의실 목록 쿼리 무효화
      queryClient.invalidateQueries({ queryKey: queryKeys.rooms.lists() });
    },
  });

  // 회의실 수정 뮤테이션
  const updateRoomMutation = useMutation({
    mutationFn: async ({ id, data }: { id: string; data: Partial<RoomFormData> }) => {
      const response = await roomsAPI.updateRoom(id, data);
      return response.data;
    },
    onSuccess: (_, { id }) => {
      // 관련 쿼리들 무효화
      queryClient.invalidateQueries({ queryKey: queryKeys.rooms.lists() });
      queryClient.invalidateQueries({ queryKey: queryKeys.rooms.detail(id) });
    },
  });

  // 회의실 삭제 뮤테이션
  const deleteRoomMutation = useMutation({
    mutationFn: roomsAPI.deleteRoom,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.rooms.lists() });
    },
  });

  return {
    // 데이터
    rooms: roomsData?.items || [],
    total: roomsData?.total || 0,
    totalPages: roomsData?.totalPages || 0,
    currentPage: roomsData?.page || 1,
    
    // 상태
    isLoading,
    error,
    
    // 액션
    refetch,
    createRoom: createRoomMutation.mutate,
    updateRoom: updateRoomMutation.mutate,
    deleteRoom: deleteRoomMutation.mutate,
    
    // 뮤테이션 상태
    isCreating: createRoomMutation.isPending,
    isUpdating: updateRoomMutation.isPending,
    isDeleting: deleteRoomMutation.isPending,
    
    // 뮤테이션 에러
    createError: createRoomMutation.error,
    updateError: updateRoomMutation.error,
    deleteError: deleteRoomMutation.error,
  };
};

/**
 * 특정 회의실 상세 정보 훅
 */
export const useRoom = (id: string) => {
  return useQuery({
    queryKey: queryKeys.rooms.detail(id),
    queryFn: async () => {
      const response = await roomsAPI.getRoom(id);
      return response.data;
    },
    enabled: !!id,
  });
};
```

### `src/hooks/useReservations.ts`
```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { reservationsAPI, usersAPI } from '@/services/api';
import { queryKeys } from '@/lib/queryClient';
import { ReservationFormData } from '@/types/reservation';
import { PaginationParams, FilterParams } from '@/types/api';

/**
 * 예약 관련 훅
 */
export const useReservations = (params?: PaginationParams & FilterParams) => {
  const queryClient = useQueryClient();

  // 예약 목록 조회
  const {
    data: reservationsData,
    isLoading,
    error,
    refetch,
  } = useQuery({
    queryKey: queryKeys.reservations.list(params || {}),
    queryFn: async () => {
      const response = await reservationsAPI.getReservations(params);
      return response.data;
    },
  });

  // 예약 생성 뮤테이션
  const createReservationMutation = useMutation({
    mutationFn: async (data: ReservationFormData) => {
      const response = await reservationsAPI.createReservation(data);
      return response.data;
    },
    onSuccess: () => {
      // 관련 쿼리들 무효화
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.lists() });
      queryClient.invalidateQueries({ queryKey: ['reservations', 'my'] });
    },
  });

  // 예약 수정 뮤테이션
  const updateReservationMutation = useMutation({
    mutationFn: async ({ id, data }: { id: string; data: Partial<ReservationFormData> }) => {
      const response = await reservationsAPI.updateReservation(id, data);
      return response.data;
    },
    onSuccess: (_, { id }) => {
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.lists() });
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.detail(id) });
      queryClient.invalidateQueries({ queryKey: ['reservations', 'my'] });
    },
  });

  // 예약 취소 뮤테이션
  const cancelReservationMutation = useMutation({
    mutationFn: reservationsAPI.cancelReservation,
    onSuccess: (_, id) => {
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.lists() });
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.detail(id) });
      queryClient.invalidateQueries({ queryKey: ['reservations', 'my'] });
    },
  });

  return {
    // 데이터
    reservations: reservationsData?.items || [],
    total: reservationsData?.total || 0,
    totalPages: reservationsData?.totalPages || 0,
    currentPage: reservationsData?.page || 1,
    
    // 상태
    isLoading,
    error,
    
    // 액션
    refetch,
    createReservation: createReservationMutation.mutate,
    updateReservation: updateReservationMutation.mutate,
    cancelReservation: cancelReservationMutation.mutate,
    
    // 뮤테이션 상태
    isCreating: createReservationMutation.isPending,
    isUpdating: updateReservationMutation.isPending,
    isCancelling: cancelReservationMutation.isPending,
    
    // 뮤테이션 에러
    createError: createReservationMutation.error,
    updateError: updateReservationMutation.error,
    cancelError: cancelReservationMutation.error,
  };
};

/**
 * 특정 예약 상세 정보 훅
 */
export const useReservation = (id: string) => {
  return useQuery({
    queryKey: queryKeys.reservations.detail(id),
    queryFn: async () => {
      const response = await reservationsAPI.getReservation(id);
      return response.data;
    },
    enabled: !!id,
  });
};

/**
 * 내 예약 목록 훅
 */
export const useMyReservations = (params?: PaginationParams) => {
  return useQuery({
    queryKey: queryKeys.reservations.my(params || {}),
    queryFn: async () => {
      const response = await usersAPI.getMyReservations(params);
      return response.data;
    },
  });
};
```

---

## 🔄 **6단계: 에러 처리 및 로딩 상태**

### `src/components/common/ApiErrorAlert.tsx`
```typescript
import { AlertTriangle } from 'lucide-react';
import { Alert, AlertDescription, AlertTitle } from '@/components/ui/alert';
import { Button } from '@/components/ui/button';
import { ApiError } from '@/services/api/client';

interface ApiErrorAlertProps {
  error: Error | ApiError | null;
  onRetry?: () => void;
  className?: string;
}

/**
 * API 에러를 표시하는 알림 컴포넌트
 */
export const ApiErrorAlert: React.FC<ApiErrorAlertProps> = ({
  error,
  onRetry,
  className,
}) => {
  if (!error) return null;

  const isApiError = error instanceof ApiError;
  const title = isApiError ? `오류 (${error.status})` : '네트워크 오류';
  const message = error.message || '알 수 없는 오류가 발생했습니다.';

  return (
    <Alert variant="destructive" className={className}>
      <AlertTriangle className="h-4 w-4" />
      <AlertTitle>{title}</AlertTitle>
      <AlertDescription className="space-y-2">
        <p>{message}</p>
        {isApiError && error.errors && (
          <div className="space-y-1">
            {Object.entries(error.errors).map(([field, messages]) => (
              <div key={field} className="text-sm">
                <strong>{field}:</strong> {messages.join(', ')}
              </div>
            ))}
          </div>
        )}
        {onRetry && (
          <Button variant="outline" size="sm" onClick={onRetry}>
            다시 시도
          </Button>
        )}
      </AlertDescription>
    </Alert>
  );
};
```

### `src/components/common/LoadingState.tsx`
```typescript
import { Skeleton } from '@/components/ui/skeleton';
import { Card, CardContent, CardHeader } from '@/components/ui/card';

interface LoadingStateProps {
  type?: 'list' | 'detail' | 'table' | 'custom';
  count?: number;
  children?: React.ReactNode;
}

/**
 * 다양한 로딩 상태를 표시하는 컴포넌트
 */
export const LoadingState: React.FC<LoadingStateProps> = ({
  type = 'list',
  count = 3,
  children,
}) => {
  if (children) {
    return <>{children}</>;
  }

  switch (type) {
    case 'list':
      return (
        <div className="space-y-4">
          {Array.from({ length: count }).map((_, index) => (
            <Card key={index}>
              <CardHeader>
                <Skeleton className="h-6 w-1/3" />
                <Skeleton className="h-4 w-2/3" />
              </CardHeader>
              <CardContent>
                <div className="space-y-2">
                  <Skeleton className="h-4 w-full" />
                  <Skeleton className="h-4 w-4/5" />
                </div>
              </CardContent>
            </Card>
          ))}
        </div>
      );

    case 'detail':
      return (
        <div className="space-y-6">
          <div className="space-y-2">
            <Skeleton className="h-8 w-1/3" />
            <Skeleton className="h-4 w-2/3" />
          </div>
          <Card>
            <CardHeader>
              <Skeleton className="h-6 w-1/4" />
            </CardHeader>
            <CardContent className="space-y-4">
              <Skeleton className="h-4 w-full" />
              <Skeleton className="h-4 w-5/6" />
              <Skeleton className="h-4 w-3/4" />
              <div className="flex gap-2">
                <Skeleton className="h-9 w-20" />
                <Skeleton className="h-9 w-16" />
              </div>
            </CardContent>
          </Card>
        </div>
      );

    case 'table':
      return (
        <div className="space-y-3">
          <Skeleton className="h-8 w-full" />
          {Array.from({ length: count }).map((_, index) => (
            <Skeleton key={index} className="h-12 w-full" />
          ))}
        </div>
      );

    default:
      return (
        <div className="space-y-2">
          {Array.from({ length: count }).map((_, index) => (
            <Skeleton key={index} className="h-4 w-full" />
          ))}
        </div>
      );
  }
};
```

---

## 🎛️ **7단계: QueryClient Provider 설정**

### `src/main.tsx` 업데이트
```typescript
import React from 'react';
import ReactDOM from 'react-dom/client';
import { QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import { queryClient } from '@/lib/queryClient';
import { apiClient } from '@/services/api/client';
import App from './App';
import '@/styles/globals.css';

// 앱 시작 시 토큰 설정
const token = localStorage.getItem('token');
if (token) {
  apiClient.setAuthToken(token);
}

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <App />
      {/* 개발 환경에서만 React Query DevTools 표시 */}
      {import.meta.env.DEV && <ReactQueryDevtools initialIsOpen={false} />}
    </QueryClientProvider>
  </React.StrictMode>
);
```

---

## 🔐 **8단계: 토큰 자동 갱신 설정**

### `src/hooks/useTokenRefresh.ts`
```typescript
import { useEffect } from 'react';
import { useQueryClient } from '@tanstack/react-query';
import { authAPI } from '@/services/api/auth';
import { apiClient } from '@/services/api/client';

/**
 * JWT 토큰 자동 갱신 훅
 */
export const useTokenRefresh = () => {
  const queryClient = useQueryClient();

  useEffect(() => {
    const refreshToken = localStorage.getItem('refreshToken');
    if (!refreshToken) return;

    // 토큰 만료 5분 전에 갱신
    const REFRESH_MARGIN = 5 * 60 * 1000; // 5분

    const scheduleTokenRefresh = () => {
      const token = localStorage.getItem('token');
      if (!token) return;

      try {
        // JWT 토큰 디코딩 (간단한 파싱)
        const payload = JSON.parse(atob(token.split('.')[1]));
        const expiryTime = payload.exp * 1000; // 밀리초로 변환
        const currentTime = Date.now();
        const timeUntilRefresh = expiryTime - currentTime - REFRESH_MARGIN;

        if (timeUntilRefresh > 0) {
          setTimeout(async () => {
            try {
              const response = await authAPI.refreshToken(refreshToken);
              const newToken = response.data.token;
              
              // 새 토큰 저장 및 설정
              localStorage.setItem('token', newToken);
              apiClient.setAuthToken(newToken);
              
              // 다음 갱신 스케줄링
              scheduleTokenRefresh();
            } catch (error) {
              // 토큰 갱신 실패 시 로그아웃 처리
              console.error('Token refresh failed:', error);
              localStorage.removeItem('token');
              localStorage.removeItem('refreshToken');
              apiClient.removeAuthToken();
              queryClient.clear();
              window.location.href = '/login';
            }
          }, timeUntilRefresh);
        }
      } catch (error) {
        console.error('Invalid token format:', error);
      }
    };

    scheduleTokenRefresh();
  }, [queryClient]);
};
```

### App 컴포넌트에 토큰 갱신 적용
```typescript
// src/App.tsx 업데이트
import { AppRouter } from '@/lib/router';
import { ErrorBoundary } from '@/components/common';
import { useTokenRefresh } from '@/hooks/useTokenRefresh';
import '@/styles/globals.css';

function App() {
  useTokenRefresh();

  return (
    <ErrorBoundary>
      <AppRouter />
    </ErrorBoundary>
  );
}

export default App;
```

---

## 🧪 **9단계: 사용 예시**

### 페이지에서 API 훅 사용하기
```typescript
// src/pages/DashboardPage.tsx 업데이트
import { PageTitle, LoadingState, ApiErrorAlert } from '@/components/common';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { useReservations } from '@/hooks/useReservations';
import { useRooms } from '@/hooks/useRooms';

export const DashboardPage: React.FC = () => {
  const { 
    reservations, 
    isLoading: reservationsLoading, 
    error: reservationsError 
  } = useReservations({ limit: 5 });
  
  const { 
    rooms, 
    isLoading: roomsLoading, 
    error: roomsError 
  } = useRooms({ limit: 10 });

  if (reservationsLoading || roomsLoading) {
    return <LoadingState type="detail" />;
  }

  return (
    <div className="space-y-6">
      <PageTitle 
        title="대시보드" 
        description="회의실 예약 현황을 한눈에 확인하세요" 
      />
      
      {/* 에러 표시 */}
      {reservationsError && (
        <ApiErrorAlert error={reservationsError} />
      )}
      {roomsError && (
        <ApiErrorAlert error={roomsError} />
      )}
      
      <div className="grid gap-4 md:grid-cols-2 lg:grid-cols-4">
        <Card>
          <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
            <CardTitle className="text-sm font-medium">
              전체 회의실
            </CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold">{rooms.length}</div>
            <p className="text-xs text-muted-foreground">
              사용 가능한 회의실
            </p>
          </CardContent>
        </Card>
        
        <Card>
          <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
            <CardTitle className="text-sm font-medium">
              오늘 예약
            </CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold">{reservations.length}</div>
            <p className="text-xs text-muted-foreground">
              진행 중인 예약
            </p>
          </CardContent>
        </Card>
        
        {/* 추가 통계 카드들... */}
      </div>
    </div>
  );
};
```

---

## 📋 **10단계: API 모킹 (개발용)**

### `src/services/api/mock.ts` (선택사항)
```typescript
import { Room } from '@/types/room';
import { Reservation } from '@/types/reservation';
import { User } from '@/types/auth';

/**
 * 개발용 목 데이터
 */
export const mockData = {
  users: [
    {
      id: '1',
      email: 'admin@example.com',
      name: '관리자',
      role: 'admin' as const,
      createdAt: '2024-01-01T00:00:00Z',
      updatedAt: '2024-01-01T00:00:00Z',
    },
  ] as User[],

  rooms: [
    {
      id: '1',
      name: '대회의실',
      description: '프레젠테이션용 대형 회의실',
      capacity: 20,
      location: '3층',
      amenities: ['프로젝터', '화이트보드', '에어컨'],
      isActive: true,
      createdAt: '2024-01-01T00:00:00Z',
      updatedAt: '2024-01-01T00:00:00Z',
    },
    {
      id: '2',
      name: '소회의실 A',
      description: '팀 미팅용 소형 회의실',
      capacity: 6,
      location: '2층',
      amenities: ['TV', '화이트보드'],
      isActive: true,
      createdAt: '2024-01-01T00:00:00Z',
      updatedAt: '2024-01-01T00:00:00Z',
    },
  ] as Room[],

  reservations: [
    {
      id: '1',
      title: '주간 팀 미팅',
      description: '개발팀 주간 스탠드업 미팅',
      startTime: '2024-06-03T09:00:00Z',
      endTime: '2024-06-03T10:00:00Z',
      status: 'confirmed' as const,
      room: {
        id: '2',
        name: '소회의실 A',
        location: '2층',
      },
      user: {
        id: '1',
        name: '관리자',
        email: 'admin@example.com',
      },
      attendees: ['개발팀'],
      createdAt: '2024-01-01T00:00:00Z',
      updatedAt: '2024-01-01T00:00:00Z',
    },
  ] as Reservation[],
};

/**
 * 개발 환경에서 API 호출을 목킹하는 헬퍼
 */
export const createMockResponse = <T>(data: T, delay = 1000): Promise<{ data: T }> => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ data });
    }, delay);
  });
};
```

---

## 🔧 **11단계: API 서비스 인덱스 파일**

### `src/services/api/index.ts`
```typescript
// API 클라이언트
export { apiClient, ApiError } from './client';
export type { ApiResponse } from './client';

// API 서비스들
export { authAPI } from './auth';
export { roomsAPI } from './rooms';
export { reservationsAPI } from './reservations';
export { usersAPI } from './users';
export type { UserStats, UpdateProfileData } from './users';

// 목 데이터 (개발용)
export { mockData, createMockResponse } from './mock';
```

---

## ✅ **완료 체크리스트**

- [x] **API 클라이언트** - Fetch 기반 HTTP 클라이언트 구현
- [x] **타입 정의** - 완전한 TypeScript 타입 시스템
- [x] **API 서비스** - 도메인별 API 서비스 분리
- [x] **TanStack Query** - 서버 상태 관리 및 캐싱
- [x] **커스텀 훅** - 재사용 가능한 데이터 페칭 훅
- [x] **에러 처리** - API 에러 및 네트워크 에러 처리
- [x] **로딩 상태** - 다양한 로딩 UI 컴포넌트
- [x] **토큰 관리** - JWT 토큰 자동 갱신
- [x] **개발 도구** - React Query DevTools 연동
- [x] **모킹 지원** - 개발용 목 데이터 시스템

---

## 📊 **API 연동 구조 요약**

### 🔄 **데이터 흐름**
1. **컴포넌트** → **커스텀 훅** → **API 서비스** → **HTTP 클라이언트**
2. **TanStack Query**로 캐싱 및 상태 관리
3. **자동 에러 처리** 및 재시도 로직
4. **토큰 자동 갱신**으로 인증 유지

### 🎯 **주요 특징**
- **타입 안전성** - 모든 API 응답에 TypeScript 타입 적용
- **효율적인 캐싱** - TanStack Query의 스마트 캐싱 전략
- **자동 동기화** - 실시간 데이터 업데이트
- **에러 복구** - 네트워크 에러 시 자동 재시도
- **개발자 친화적** - DevTools와 모킹 지원

**📋 API 연동이 완료되었습니다!** 🎉

4번 문서 확인 완료입니다! 혹시 수정이나 추가가 필요한 부분이 있으시면 말씀해 주세요.### `src/services/api/client.ts`
```typescript
import { API_BASE_URL } from '@/utils/constants';

/**
 * API 응답 타입 정의
 */
export interface ApiResponse<T = any> {
  success: boolean;
  data: T;
  message?: string;
  errors?: Record<string, string[]>;
}

/**
 * API 에러 클래스
 */
export class ApiError extends Error {
  constructor(
    public status: number,
    public message: string,
    public errors?: Record<string, string[]>
  ) {
    super(message);
    this.name = 'ApiError';
  }
}

/**
 * HTTP 메서드 타입
 */
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH';

/**
 * 요청 옵션 인터페이스
 */
interface RequestOptions {
  method?: HttpMethod;
  headers?: Record<string, string>;
  body?: any;
  params?: Record<string, string | number>;
}

/**
 * 기본 HTTP 클라이언트 클래스
 */
export class ApiClient {
  private baseURL: string;
  private defaultHeaders: Record<string, string>;

  constructor(baseURL: string) {
    this.baseURL = baseURL;
    this.defaultHeaders = {
      'Content-Type': 'application/json',
    };
  }

  /**
   * 인증 토큰 설정
   */
  setAuthToken(token: string): void {
    this.defaultHeaders.Authorization = `Bearer ${token}`;
  }

  /**
   * 인증 토큰 제거
   */
  removeAuthToken(): void {
    delete this.defaultHeaders.Authorization;
  }

  /**
   * URL 파라미터 생성
   */
  private buildURL(endpoint: string, params?: Record<string, string | number>): string {
    const url = new URL(endpoint, this.baseURL);
    
    if (params) {
      Object.entries(params).forEach(([key, value]) => {
        url.searchParams.append(key, String(value));
      });
    }
    
    return url.toString();
  }

  /**
   * HTTP 요청 실행
   */
  private async request<T>(
    endpoint: string,
    options: RequestOptions = {}
  ): Promise<ApiResponse<T>> {
    const {
      method = 'GET',
      headers = {},
      body,
      params,
    } = options;

    const url = this.buildURL(endpoint, params);
    const config: RequestInit = {
      method,
      headers: {
        ...this.defaultHeaders,
        ...headers,
      },
    };

    // Body 처리
    if (body) {
      if (body instanceof FormData) {
        // FormData인 경우 Content-Type 헤더 제거 (브라우저가 자동 설정)
        delete config.headers!['Content-Type'];
        config.body = body;
      } else {
        config.body = JSON.stringify(body);
      }
    }

    try {
      const response = await fetch(url, config);
      
      // 응답 데이터 파싱
      let data: ApiResponse<T>;
      const contentType = response.headers.get('content-type');
      
      if (contentType && contentType.includes('application/json')) {
        data = await response.json();
      } else {
        // JSON이 아닌 경우 텍스트로 처리
        const text = await response.text();
        data = {
          success: response.ok,
          data: text as T,
        };
      }

      // 에러 응답 처리
      if (!response.ok) {
        throw new ApiError(
          response.status,
          data.message || `HTTP ${response.status}: ${response.statusText}`,
          data.errors
        );
      }

      return data;
    } catch (error) {
      // 네트워크 에러 등 처리
      if (error instanceof ApiError) {
        throw error;
      }
      
      throw new ApiError(
        0,
        error instanceof Error ? error.message : '알 수 없는 에러가 발생했습니다.'
      );
    }
  }

  /**
   * GET 요청
   */
  async get<T>(endpoint: string, params?: Record<string, string | number>): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'GET', params });
  }

  /**
   * POST 요청
   */
  async post<T>(endpoint: string, body?: any): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'POST', body });
  }

  /**
   * PUT 요청
   */
  async put<T>(endpoint: string, body?: any): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'PUT', body });
  }

  /**
   * DELETE 요청
   */
  async delete<T>(endpoint: string): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'DELETE' });
  }

  /**
   * PATCH 요청
   */
  async patch<T>(endpoint: string, body?: any): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'PATCH', body });
  }
}

/**
 * 기본 API 클라이언트 인스턴스
 */
export const apiClient = new ApiClient(API_BASE_URL);
```

### `src/utils/constants.ts` 업데이트
```typescript
// 기존 상수에 추가
export const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:3001/api';
export const WS_URL = import.meta.env.VITE_WS_URL || 'ws://localhost:3001';

/**
 * API 엔드포인트 상수
 */
export const API_ENDPOINTS = {
  // 인증
  AUTH: {
    LOGIN: '/auth/login',
    LOGOUT: '/auth/logout',
    REFRESH: '/auth/refresh',
    ME: '/auth/me',
  },
  
  // 회의실
  ROOMS: {
    LIST: '/rooms',
    CREATE: '/rooms',
    DETAIL: (id: string) => `/rooms/${id}`,
    UPDATE: (id: string) => `/rooms/${id}`,
    DELETE: (id: string) => `/rooms/${id}`,
  },
  
  // 예약
  RESERVATIONS: {
    LIST: '/reservations',
    CREATE: '/reservations',
    DETAIL: (id: string) => `/reservations/${id}`,
    UPDATE: (id: string) => `/reservations/${id}`,
    DELETE: (id: string) => `/reservations/${id}`,
  },
  
  // 사용자
  USERS: {
    MY_RESERVATIONS: '/users/me/reservations',
    UPDATE_PROFILE: '/users/me',
    STATS: '/users/me/stats',
  },
} as const;
```

---

## 📝 **2단계: TypeScript 타입 정의**

### `src/types/api.ts`
```typescript
/**
 * 페이지네이션 매개변수
 */
export interface PaginationParams {
  page?: number;
  limit?: number;
  sort?: string;
  order?: 'asc' | 'desc';
}

/**
 * 페이지네이션 응답
 */
export interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  limit: number;
  totalPages: number;
}

/**
 * 필터 매개변수
 */
export interface FilterParams {
  search?: string;
  startDate?: string;
  endDate?: string;
  status?: string;
  roomId?: string;
}
```

### `src/types/auth.ts`
```typescript
/**
 * 로그인 요청 데이터
 */
export interface LoginRequest {
  email: string;
  password: string;
}

/**
 * 로그인 응답 데이터
 */
export interface LoginResponse {
  user: User;
  token: string;
  refreshToken: string;
}

/**
 * 사용자 정보
 */
export interface User {
  id: string;
  email: string;
  name: string;
  role: 'admin' | 'user';
  avatar?: string;
  createdAt: string;
  updatedAt: string;
}
```

### `src/types/room.ts`
```typescript
/**
 * 회의실 정보
 */
export interface Room {
  id: string;
  name: string;
  description?: string;
  capacity: number;
  location: string;
  amenities: string[];
  isActive: boolean;
  createdAt: string;
  updatedAt: string;
}

/**
 * 회의실 생성/수정 요청 데이터
 */
export interface RoomFormData {
  name: string;
  description?: string;
  capacity: number;
  location: string;
  amenities: string[];
  isActive?: boolean;
}
```

### `src/types/reservation.ts`
```typescript
import { Room } from './room';
import { User } from './auth';

/**
 * 예약 상태
 */
export type ReservationStatus = 'pending' | 'confirmed' | 'cancelled' | 'completed';

/**
 * 예약 정보
 */
export interface Reservation {
  id: string;
  title: string;
  description?: string;
  startTime: string;
  endTime: string;
  status: ReservationStatus;
  room: Room;
  user: User;
  attendees?: string[];
  createdAt: string;
  updatedAt: string;
}

/**
 * 예약 생성/수정 요청 데이터
 */
export interface ReservationFormData {
  title: string;
  description?: string;
  roomId: string;
  startTime: string;
  endTime: string;
  attendees?: string[];
}
```

---

## 🔌 **3단계: API 서비스 구현**

### `src/services/api/auth.ts`
```typescript
import { apiClient, ApiResponse } from './client';
import { API_ENDPOINTS } from '@/utils/constants';
import { LoginRequest, LoginResponse, User } from '@/types/auth';

/**
 * 인증 관련 API 서비스
 */
export const authAPI = {
  /**
   * 로그인
   */
  login: async (credentials: LoginRequest): Promise<ApiResponse<LoginResponse>> => {
    return apiClient.post<LoginResponse>(API_ENDPOINTS.AUTH.LOGIN, credentials);
  },

  /**
   * 로그아웃
   */
  logout: async (): Promise<ApiResponse<void>> => {
    return apiClient.post<void>(API_ENDPOINTS.AUTH.LOGOUT);
  },

  /**
   * 토큰 갱신
   */
  refreshToken: async (refreshToken: string): Promise<ApiResponse<{ token: string }>> => {
    return apiClient.post<{ token: string }>(API_ENDPOINTS.AUTH.REFRESH, {
      refreshToken,
    });
  },

  /**
   * 현재 사용자 정보 조회
   */
  getMe: async (): Promise<ApiResponse<User>> => {
    return apiClient.get<User>(API_ENDPOINTS.AUTH.ME);
  },
};
```

### `src/services/api/rooms.ts`
```typescript
import { apiClient, ApiResponse } from './client';
import { API_ENDPOINTS } from '@/utils/constants';
import { Room, RoomFormData } from '@/types/room';
import { PaginatedResponse, PaginationParams, FilterParams } from '@/types/api';

/**
 * 회의실 관련 API 서비스
 */
export const roomsAPI = {
  /**
   * 회의실 목록 조회
   */
  getRooms: async (
    params?: PaginationParams & FilterParams
  ): Promise<ApiResponse<PaginatedResponse<Room>>> => {
    return apiClient.get<PaginatedResponse<Room>>(API_ENDPOINTS.ROOMS.LIST, params);
  },

  /**
   * 회의실 상세 조회
   */
  getRoom: async (id: string): Promise<ApiResponse<Room>> => {
    return apiClient.get<Room>(API_ENDPOINTS.ROOMS.DETAIL(id));
  },

  /**
   * 회의실 생성
   */
  createRoom: async (data: RoomFormData): Promise<ApiResponse<Room>> => {
    return apiClient.post<Room>(API_ENDPOINTS.ROOMS.CREATE, data);
  },

  /**
   * 회의실 수정
   */
  updateRoom: async (id: string, data: Partial<RoomFormData>): Promise<ApiResponse<Room>> => {
    return apiClient.put<Room>(API_ENDPOINTS.ROOMS.UPDATE(id), data);
  },

  /**
   * 회의실 삭제
   */
  deleteRoom: async (id: string): Promise<ApiResponse<void>> => {
    return apiClient.delete<void>(API_ENDPOINTS.ROOMS.DELETE(id));
  },
};
```

### `src/services/api/reservations.ts`
```typescript
import { apiClient, ApiResponse } from './client';
import { API_ENDPOINTS } from '@/utils/constants';
import { Reservation, ReservationFormData } from '@/types/reservation';
import { PaginatedResponse, PaginationParams, FilterParams } from '@/types/api';

/**
 * 예약 관련 API 서비스
 */
export const reservationsAPI = {
  /**
   * 예약 목록 조회
   */
  getReservations: async (
    params?: PaginationParams & FilterParams
  ): Promise<ApiResponse<PaginatedResponse<Reservation>>> => {
    return apiClient.get<PaginatedResponse<Reservation>>(API_ENDPOINTS.RESERVATIONS.LIST, params);
  },

  /**
   * 예약 상세 조회
   */
  getReservation: async (id: string): Promise<ApiResponse<Reservation>> => {
    return apiClient.get<Reservation>(API_ENDPOINTS.RESERVATIONS.DETAIL(id));
  },

  /**
   * 예약 생성
   */
  createReservation: async (data: ReservationFormData): Promise<ApiResponse<Reservation>> => {
    return apiClient.post<Reservation>(API_ENDPOINTS.RESERVATIONS.CREATE, data);
  },

  /**
   * 예약 수정
   */
  updateReservation: async (
    id: string,
    data: Partial<ReservationFormData>
  ): Promise<ApiResponse<Reservation>> => {
    return apiClient.put<Reservation>(API_ENDPOINTS.RESERVATIONS.UPDATE(id), data);
  },

  /**
   * 예약 취소
   */
  cancelReservation: async (id: string): Promise<ApiResponse<Reservation>> => {
    return apiClient.patch<Reservation>(API_ENDPOINTS.RESERVATIONS.UPDATE(id), {
      status: 'cancelled',
    });
  },

  /**
   * 예약 삭제
   */
  deleteReservation: async (id: string): Promise<ApiResponse<void>> => {
    return apiClient.delete<void>(API_ENDPOINTS.RESERVATIONS.DELETE(id));
  },
};
```

### `src/services/api/users.ts`
```typescript
import { apiClient, ApiResponse } from './client';
import { API_ENDPOINTS } from '@/utils/constants';
import { User } from '@/types/auth';
import { Reservation } from '@/types/reservation';
import { PaginatedResponse, PaginationParams } from '@/types/api';

/**
 * 사용자 통계 정보
 */
export interface UserStats {
  totalReservations: number;
  upcomingReservations: number;
  completedReservations: number;
  cancelledReservations: number;
}

/**
 * 프로필 업데이트 데이터
 */
export interface UpdateProfileData {
  name?: string;
  avatar?: File;
}

/**
 * 사용자 관련 API 서비스
 */
export const usersAPI = {
  /**
   * 내 예약 목록 조회
   */
  getMyReservations: async (
    params?: PaginationParams
  ): Promise<ApiResponse<PaginatedResponse<Reservation>>> => {
    return apiClient.get<PaginatedResponse<Reservation>>(
      API_ENDPOINTS.USERS.MY_RESERVATIONS,
      params
    );
  },

  /**
   * 프로필 업데이트
   */
  updateProfile: async (data: UpdateProfileData): Promise<ApiResponse<User>> => {
    // 파일 업로드가 있는 경우 FormData 사용
    if (data.avatar) {
      const formData = new FormData();
      if (data.name) formData.append('name', data.name);
      formData.append('avatar', data.avatar);
      return apiClient.put<User>(API_ENDPOINTS.USERS.UPDATE_PROFILE, formData);
    }

    return apiClient.put<User>(API_ENDPOINTS.USERS.UPDATE_PROFILE, data);
  },

  /**
   * 사용자 통계 조회
   */
  getStats: async (): Promise<ApiResponse<UserStats>> => {
    return apiClient.get<UserStats>(API_ENDPOINTS.USERS.STATS);
  },
};
```

---

## ⚡ **4단계: TanStack Query 설정**

### `src/lib/queryClient.ts`
```typescript
import { QueryClient, DefaultOptions } from '@tanstack/react-query';
import { ApiError } from '@/services/api/client';

/**
 * 기본 쿼리 옵션
 */
const queryConfig: DefaultOptions = {
  queries: {
    // 에러 재시도 설정
    retry: (failureCount, error) => {
      // 인증 에러(401, 403)는 재시도하지 않음
      if (error instanceof ApiError && [401, 403].includes(error.status)) {
        return false;
      }
      // 클라이언트 에러(4xx)는 재시도하지 않음
      if (error instanceof ApiError && error.status >= 400 && error.status < 500) {
        return false;
      }
      // 최대 3번까지 재시도
      return failureCount < 3;
    },
    
    // 백그라운드에서 자동 재검증 (5분)
    staleTime: 5 * 60 * 1000,
    
    // 캐시 유지 시간 (10분)
    gcTime: 10 * 60 * 1000,
    
    // 윈도우 포커스 시 재검증
    refetchOnWindowFocus: false,
    
    // 네트워크 재연결 시 재검증
    refetchOnReconnect: true,
  },
  
  mutations: {
    // 뮤테이션 에러 재시도 (1번만)
    retry: 1,
  },
};

/**
 * QueryClient 인스턴스 생성
 */
export const queryClient = new QueryClient({
  defaultOptions: queryConfig,
});

/**
 * 쿼리 키 팩토리
 */
export const queryKeys = {
  // 인증
  auth: {
    me: ['auth', 'me'] as const,
  },
  
  // 회의실
  rooms: {
    all: ['rooms'] as const,
    lists: () => [...queryKeys.rooms.all, 'list'] as const,
    list: (params: Record<string, any>) => [...queryKeys.rooms.lists(), params] as const,
    details: () => [...queryKeys.rooms.all, 'detail'] as const,
    detail: (id: string) => [...queryKeys.rooms.details(), id] as const,
  },
  
  // 예약
  reservations: {
    all: ['reservations'] as const,
    lists: () => [...queryKeys.reservations.all, 'list'] as const,
    list: (params: Record<string, any>) => [...queryKeys.reservations.lists(), params] as const,
    details: () => [...queryKeys.reservations.all, 'detail'] as const,
    detail: (id: string) => [...queryKeys.reservations.details(), id] as const,
    my: (params: Record<string, any>) => ['reservations', 'my', params] as const,
  },
  
  // 사용자
  users: {
    stats: ['users', 'stats'] as const,
  },
} as const;
```

---

## 🎣 **5단계: 커스텀 훅 구현**

### `src/hooks/useAuth.ts`
```typescript
import { useMutation, useQuery, useQueryClient } from '@tanstack/react-query';
import { authAPI } from '@/services/api/auth';
import { apiClient } from '@/services/api/client';
import { queryKeys } from '@/lib/queryClient';
import { LoginRequest } from '@/types/auth';
import { useNavigate } from 'react-router-dom';

/**
 * 인증 관련 훅
 */
export const useAuth = () => {
  const queryClient = useQueryClient();
  const navigate = useNavigate();

  // 현재 사용자 정보 조회
  const { data: user, isLoading, error } = useQuery({
    queryKey: queryKeys.auth.me,
    queryFn: async () => {
      const response = await authAPI.getMe();
      return response.data;
    },
    enabled: !!localStorage.getItem('token'), // 토큰이 있을 때만 실행
  });

  // 로그인 뮤테이션
  const loginMutation = useMutation({
    mutationFn: async (credentials: LoginRequest) => {
      const response = await authAPI.login(credentials);
      return response.data;
    },
    onSuccess: (data) => {
      // 토큰 저장 및 API 클라이언트에 설정
      localStorage.setItem('token', data.token);
      localStorage.setItem('refreshToken', data.refreshToken);
      apiClient.setAuthToken(data.token);
      
      // 사용자 정보 캐시 업데이트
      queryClient.setQueryData(queryKeys.auth.me, data.user);
      
      // 대시보드로 이동
      navigate('/dashboard');
    },
  });

  // 로그아웃 뮤테이션
  const logoutMutation = useMutation({
    mutationFn: authAPI.logout,
    onSuccess: () => {
      // 토큰 제거
      localStorage.removeItem('token');
      localStorage.removeItem('refreshToken');
      apiClient.removeAuthToken();
      
      // 모든 쿼리 캐시 초기화
      queryClient.clear();
      
      // 로그인 페이지로 이동
      navigate('/login');
    },
  });

  return {
    // 상태
    user,
    isLoading,
    error,
    isAuthenticated: !!user,
    
    // 액션
    login: loginMutation.mutate,
    logout: logoutMutation.mutate,
    
    // 로딩 상태
    isLoggingIn: loginMutation.isPending,
    isLoggingOut: logoutMutation.isPending,
    
    // 에러
    loginError: loginMutation.error,
    logoutError: logoutMutation.error,
  };
};
```

### `src/hooks/useRooms.ts`
```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { roomsAPI } from '@/services/api/rooms';
import { queryKeys } from '@/lib/queryClient';
import { RoomFormData } from '@/types/room';
import { PaginationParams, FilterParams } from '@/types/api';

/**
 * 회의실 관련 훅
 */
export const useRooms = (params?: PaginationParams & FilterParams) => {
  const queryClient = useQueryClient();

  // 회의실 목록 조회
  const {
    data: roomsData,
    isLoading,
    error,
    refetch,
  } = useQuery({
    queryKey: queryKeys.rooms.list(params || {}),
    queryFn: async () => {
      const response = await roomsAPI.getRooms(params);
      return response.data;
    },
  });

  // 회의실 생성 뮤테이션
  const createRoomMutation = useMutation({
    mutationFn: async (data: RoomFormData) => {
      const response = await roomsAPI.createRoom(data);
      return response.data;
    },
    onSuccess: () => {
      // 회의실 목록 쿼리 무효화
      queryClient.invalidateQueries({ queryKey: queryKeys.rooms.lists() });
    },
  });

  // 회의실 수정 뮤테이션
  const updateRoomMutation = useMutation({
    mutationFn: async ({ id, data }: { id: string; data: Partial<RoomFormData> }) => {
      const response = await roomsAPI.updateRoom(id, data);
      return response.data;
    },
    onSuccess: (_, { id }) => {
      // 관련 쿼리들 무효화
      queryClient.invalidateQueries({ queryKey: queryKeys.rooms.lists() });
      queryClient.invalidateQueries({ queryKey: queryKeys.rooms.detail(id) });
    },
  });

  // 회의실 삭제 뮤테이션
  const deleteRoomMutation = useMutation({
    mutationFn: roomsAPI.deleteRoom,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.rooms.lists() });
    },
  });

  return {
    // 데이터
    rooms: roomsData?.items || [],
    total: roomsData?.total || 0,
    totalPages: roomsData?.totalPages || 0,
    currentPage: roomsData?.page || 1,
    
    // 상태
    isLoading,
    error,
    
    // 액션
    refetch,
    createRoom: createRoomMutation.mutate,
    updateRoom: updateRoomMutation.mutate,
    deleteRoom: deleteRoomMutation.mutate,
    
    // 뮤테이션 상태
    isCreating: createRoomMutation.isPending,
    isUpdating: updateRoomMutation.isPending,
    isDeleting: deleteRoomMutation.isPending,
    
    // 뮤테이션 에러
    createError: createRoomMutation.error,
    updateError: updateRoomMutation.error,
    deleteError: deleteRoomMutation.error,
  };
};

/**
 * 특정 회의실 상세 정보 훅
 */
export const useRoom = (id: string) => {
  return useQuery({
    queryKey: queryKeys.rooms.detail(id),
    queryFn: async () => {
      const response = await roomsAPI.getRoom(id);
      return response.data;
    },
    enabled: !!id,
  });
};
```

### `src/hooks/useReservations.ts`
```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { reservationsAPI } from '@/services/api/reservations';
import { queryKeys } from '@/lib/queryClient';
import { ReservationFormData } from '@/types/reservation';
import { PaginationParams, FilterParams } from '@/types/api';

/**
 * 예약 관련 훅
 */
export const useReservations = (params?: PaginationParams & FilterParams) => {
  const queryClient = useQueryClient();

  // 예약 목록 조회
  const {
    data: reservationsData,
    isLoading,
    error,
    refetch,
  } = useQuery({
    queryKey: queryKeys.reservations.list(params || {}),
    queryFn: async () => {
      const response = await reservationsAPI.getReservations(params);
      return response.data;
    },
  });

  // 예약 생성 뮤테이션
  const createReservationMutation = useMutation({
    mutationFn: async (data: ReservationFormData) => {
      const response = await reservationsAPI.createReservation(data);
      return response.data;
    },
    onSuccess: () => {
      // 관련 쿼리들 무효화
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.lists() });
      queryClient.invalidateQueries({ queryKey: ['reservations', 'my'] });
    },
  });

  // 예약 수정 뮤테이션
  const updateReservationMutation = useMutation({
    mutationFn: async ({ id, data }: { id: string; data: Partial<ReservationFormData> }) => {
      const response = await reservationsAPI.updateReservation(id, data);
      return response.data;
    },
    onSuccess: (_, { id }) => {
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.lists() });
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.detail(id) });
      queryClient.invalidateQueries({ queryKey: ['reservations', 'my'] });
    },
  });

  // 예약 취소 뮤테이션
  const cancelReservationMutation = useMutation({
    mutationFn: reservationsAPI.cancelReservation,
    onSuccess: (_, id) => {
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.lists() });
      queryClient.invalidateQueries({ queryKey: queryKeys.reservations.detail(id) });
      queryClient.invalidateQueries({ queryKey: ['reservations', 'my'] });
    },
  });

  return {
    // 데이터
    reservations: reservationsData?.items || [],
    total: reservationsData?.total || 0,
    totalPages: reservationsData?.totalPages || 0,
    currentPage: reservationsData?.page || 1,
    
    // 상태
    isLoading,
    error,
    
    // 액션
    refetch,
    createReservation: createReservationMutation.mutate,
    updateReservation: updateReservationMutation.mutate,
    cancelReservation: cancelReservationMutation.mutate,
    
    // 뮤테이션 상태
    isCreating: createReservationMutation.isPending,
    isUpdating: updateReservationMutation.isPending,
    isCancelling: cancelReservationMutation.isPending,
    
    // 뮤테이션 에러
    createError: createReservationMutation.error,
    updateError: updateReservationMutation.error,
    cancelError: cancelReservationMutation.error,
  };
};

/**
 * 특정 예약 상세 정보 훅
 */
export const useReservation = (id: string) => {
  return useQuery({
    queryKey: queryKeys.reservations.detail(id),
    queryFn: async () => {
      const response = await reservationsAPI.getReservation(id);
      return response.data;
    },
    enabled: !!id,
  });
};

/**
 * 내 예약 목록 훅
 */
export const useMyReservations = (params?: PaginationParams) => {
  return useQuery({
    queryKey: queryKeys.reservations.my(params || {}),
    queryFn: async () => {
      const response = await usersAPI.getMyReservations(params);
      return response.data;
    },
  });
};
```

---

## 🔄 **6단계: 에러 처리 및 로딩 상태**

### `src/components/common/ApiErrorAlert.tsx`
```typescript
import { AlertTriangle } from 'lucide-react';
import { Alert, AlertDescription, AlertTitle } from '@/components/ui/alert';
import { Button } from '@/components/ui/button';
import { ApiError } from '@/services/api/client';

interface ApiErrorAlertProps {
  error: Error | ApiError | null;
  onRetry?: () => void;
  className?: string;
}

/**
 * API 에러를 표시하는 알림 컴포넌트
 */
export const ApiErrorAlert: React.FC<ApiErrorAlertProps> = ({
  error,
  onRetry,
  className,
}) => {
  if (!error) return null;

  const isApiError = error instanceof ApiError;
  const title = isApiError ? `오류 (${error.status})` : '네트워크 오류';
  const message = error.message || '알 수 없는 오류가 발생했습니다.';

  return (
    <Alert variant="destructive" className={className}>
      <AlertTriangle className="h-4 w-4" />
      <AlertTitle>{title}</AlertTitle>
      <AlertDescription className="space-y-2">
        <p>{message}</p>
        {isApiError && error.errors && (
          <div className="space-y-1">
            {Object.entries(error.errors).map(([field, messages]) => (
              <div key={field} className="text-sm">
                <strong>{field}:</strong> {messages.join(', ')}
              </div>
            ))}
          </div>
        )}
        {onRetry && (
          <Button variant="outline" size="sm" onClick={onRetry}>
            다시 시도
          </Button>
        )}
      </AlertDescription>
    </Alert>
  );
};
```

### `src/components/common/LoadingState.tsx`
```typescript
import { Skeleton } from '@/components/ui/skeleton';
import { Card, CardContent, CardHeader } from '@/components/ui/card';

interface LoadingStateProps {
  type?: 'list' | 'detail' | 'table' | 'custom';
  count?: number;
  children?: React.ReactNode;
}

/**
 * 다양한 로딩 상태를 표시하는 컴포넌트
 */
export const LoadingState: React.FC<LoadingStateProps> = ({
  type = 'list',
  count = 3,
  children,
}) => {
  if (children) {
    return <>{children}</>;
  }

  switch (type) {
    case 'list':
      return (
        <div className="space-y-4">
          {Array.from({ length: count }).map((_, index) => (
            <Card key={index}>
              <CardHeader>
                <Skeleton className="h-6 w-1/3" />
                <Skeleton className="h-4 w-2/3" />
              </CardHeader>
              <CardContent>
                <div className="space-y-2">
                  <Skeleton className="h-4 w-full" />
                  <Skeleton className="h-4 w-4/5" />
                </div>
              </CardContent>
            </Card>
          ))}
        </div>
      );

    case 'detail':
      return (
        <div className="space-y-6">
          <div className="space-y-2">
            <Skeleton className="h-8 w-1/3" />
            <Skeleton className="h-4 w-2/3" />
          </div>
          <Card>
            <CardHeader>
              <Skeleton className="h-6 w-1/4" />
            </CardHeader>
            <CardContent className="space-y-4">
              <Skeleton className="h-4 w-full" />
              <Skeleton className="h-4 w-5/6" />
              <Skeleton className="h-4 w-3/4" />
              <div className="flex gap-2">
                <Skeleton className="h-9 w-20" />
                <Skeleton className="h-9 w-16" />
              </div>
            </CardContent>
          </Card>
        </div>
      );

    case 'table':
      return (
        <div className="space-y-3">
          <Skeleton className="h-8 w-full" />
          {Array.from({ length: count }).map((_, index) => (
            <Skeleton key={index} className="h-12 w-full" />
          ))}
        </div>
      );

    default:
      return (
        <div className="space-y-2">
          {Array.from({ length: count }).map((_, index) => (
            <Skeleton key={index} className="h-4 w-full" />
          ))}
        </div>
      );
  }
};
```

---

## 🎛️ **7단계: QueryClient Provider 설정**

### `src/main.tsx` 업데이트
```typescript
import React from 'react';
import ReactDOM from 'react-dom/client';
import { QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import { queryClient } from '@/lib/queryClient';
import { apiClient } from '@/services/api/client';
import App from './App';
import '@/styles/globals.css';

// 앱 시작 시 토큰 설정
const token = localStorage.getItem('token');
if (token) {
  apiClient.setAuthToken(token);
}

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <App />
      {/* 개발 환경에서만 React Query DevTools 표시 */}
      {import.meta.env.DEV && <ReactQueryDevtools initialIsOpen={false} />}
    </QueryClientProvider>
  </React.StrictMode>
);
```

---

## 🔐 **8단계: 토큰 자동 갱신 설정**

### `src/hooks/useTokenRefresh.ts`
```typescript
import { useEffect } from 'react';
import { useQueryClient } from '@tanstack/react-query';
import { authAPI } from '@/services/api/auth';
import { apiClient } from '@/services/api/client';

/**
 * JWT 토큰 자동 갱신 훅
 */
export const useTokenRefresh = () => {
  const queryClient = useQueryClient();

  useEffect(() => {
    const refreshToken = localStorage.getItem('refreshToken');
    if (!refreshToken) return;

    // 토큰 만료 5분 전에 갱신
    const REFRESH_MARGIN = 5 * 60 * 1000; // 5분

    const scheduleTokenRefresh = () => {
      const token = localStorage.getItem('token');
      if (!token) return;

      try {
        // JWT 토큰 디코딩 (간단한 파싱)
        const payload = JSON.parse(atob(token.split('.')[1]));
        const expiryTime = payload.exp * 1000; // 밀리초로 변환
        const currentTime = Date.now();
        const timeUntilRefresh = expiryTime - currentTime - REFRESH_MARGIN;

        if (timeUntilRefresh > 0) {
          setTimeout(async () => {
            try {
              const response = await authAPI.refreshToken(refreshToken);
              const newToken = response.data.token;
              
              // 새 토큰 저장 및 설정
              localStorage.setItem('token', newToken);
              apiClient.setAuthToken(newToken);
              
              // 다음 갱신 스케줄링
              scheduleTokenRefresh();
            } catch (error) {
              // 토큰 갱신 실패 시 로그아웃 처리
              console.error('Token refresh failed:', error);
              localStorage.removeItem('token');
              localStorage.removeItem('refreshToken');
              apiClient.removeAuthToken();
              queryClient.clear();
              window.location.href = '/login';
            }
          }, timeUntilRefresh);
        }
      } catch (error) {
        console.error('Invalid token format:', error);
      }
    };

    scheduleTokenRefresh();
  }, [queryClient]);
};
```

### App 컴포넌트에 토큰 갱신 적용
```typescript
// src/App.tsx 업데이트
import { AppRouter } from '@/lib/router';
import { ErrorBoundary } from '@/components/common';
import { useTokenRefresh } from '@/hooks/useTokenRefresh';
import '@/styles/globals.css';

function App() {
  useTokenRefresh();

  return (
    <ErrorBoundary>
      <AppRouter />
    </ErrorBoundary>
  );
}

export default App;
```

---

## 🧪 **9단계: 사용 예시**

### 페이지에서 API 훅 사용하기
```typescript
// src/pages/DashboardPage.tsx 업데이트
import { PageTitle, LoadingState, ApiErrorAlert } from '@/components/common';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { useReservations } from '@/hooks/useReservations';
import { useRooms } from '@/hooks/useRooms';

export const DashboardPage: React.FC = () => {
  const { 
    reservations, 
    isLoading: reservationsLoading, 
    error: reservationsError 
  } = useReservations({ limit: 5 });
  
  const { 
    rooms, 
    isLoading: roomsLoading, 
    error: roomsError 
  } = useRooms({ limit: 10 });

  if (reservationsLoading || roomsLoading) {
    return <LoadingState type="detail" />;
  }

  return (
    <div className="space-y-6">
      <PageTitle 
        title="대시보드" 
        description="회의실 예약 현황을 한눈에 확인하세요" 
      />
      
      {/* 에러 표시 */}
      {reservationsError && (
        <ApiErrorAlert error={reservationsError} />
      )}
      {roomsError && (
        <ApiErrorAlert error={roomsError} />
      )}
      
      <div className="grid gap-4 md:grid-cols-2 lg:grid-cols-4">
        <Card>
          <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
            <CardTitle className="text-sm font-medium">
              전체 회의실
            </CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold">{rooms.length}</div>
            <p className="text-xs text-muted-foreground">
              사용 가능한 회의실
            </p>
          </CardContent>
        </Card>
        
        <Card>
          <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
            <CardTitle className="text-sm font-medium">
              오늘 예약
            </CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold">{reservations.length}</div>
            <p className="text-xs text-muted-foreground">
              진행 중인 예약
            </p>
          </CardContent>
        </Card>
        
        {/* 추가 통계 카드들... */}
      </div>
    </div>
  );
};
```

---

## 📋 **10단계: API 모킹 (개발용)**

### `src/services/api/mock.ts` (선택사항)
```typescript
import { Room } from '@/types/room';
import { Reservation } from '@/types/reservation';
import { User } from '@/types/auth';

/**
 * 개발용 목 데이터
 */
export const mockData = {
  users: [
    {
      id: '1',
      email: 'admin@example.com',
      name: '관리자',
      role: 'admin' as const,
      createdAt: '2024-01-01T00:00:00Z',
      updatedAt: '2024-01-01T00:00:00Z',
    },
  ] as User[],

  rooms: [
    {
      id: '1',
      name: '대회의실',
      description: '프레젠테이션용 대형 회의실',
      capacity: 20,
      location: '3층',
      amenities: ['프로젝터', '화이트보드', '에어컨'],
      isActive: true,
      createdAt: '2024-01-01T00:00:00Z',
      updatedAt: '2024-01-01T00:00:00Z',
    },
    {
      id: '2',
      name: '소회의실 A',
      description: '팀 미팅용 소형 회의실',
      capacity: 6,
      location: '2층',
      amenities: ['TV', '화이트보드'],
      isActive: true,
      createdAt: '2024-01-01T00:00:00Z',
      updatedAt: '2024-01-01T00:00:00Z',
    },
  ] as Room[],

  reservations: [
    {
      id: '1',
      title: '주간 팀 미팅',
      description: '개발팀 주간 스탠드업 미팅',
      startTime: '2024-06-03T09:00:00Z',
      endTime: '2024-06-03T10:00:00Z',
      status: 'confirmed' as const,
      room: {
        id: '2',
        name: '소회의실 A',
        location: '2층',
      },
      user: {
        id: '1',
        name: '관리자',
        email: 'admin@example.com',
      },
      attendees: ['개발팀'],
      createdAt: '2024-01-01T00:00:00Z',
      updatedAt: '2024-01-01T00:00:00Z',
    },
  ] as Reservation[],
};

/**
 * 개발 환경에서 API 호출을 목킹하는 헬퍼
 */
export const createMockResponse = <T>(data: T, delay = 1000): Promise<{ data: T }> => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ data });
    }, delay);
  });
};
```
