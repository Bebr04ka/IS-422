<template>
  <div class="subnet-calculator">
    <div class="calculator-card">
      <h2 class="calculator-title">Калькулятор подсетей</h2>
      
      <div class="input-group">
        <label class="input-label" for="ip-address">IP адрес:</label>
        <input
          id="ip-address"
          v-model="ipAddress"
          type="text"
          placeholder="192.168.1.150"
          class="input-field"
          :class="{ 'input-error': !isIpValid(ipAddress) && ipAddress !== '' }"
          @keyup.enter="calculate"
        />
        <div v-if="!isIpValid(ipAddress) && ipAddress !== ''" class="error-message">
          Неверный формат IP адреса
        </div>
      </div>

      <div class="input-group">
        <label class="input-label" for="network-mask">Сетевая маска:</label>
        <select
          id="network-mask"
          v-model="selectedMask"
          class="select-field"
        >
          <option
            v-for="option in MASK_OPTIONS"
            :key="option.cidr"
            :value="option.value"
          >
            {{ option.label }}
          </option>
        </select>
      </div>

      <button
        class="calculate-button"
        :disabled="!isFormValid"
        @click="calculate"
      >
        Рассчитать
      </button>

      <div v-if="showResults" class="results-section">
        <h3 class="results-title">Результаты расчета:</h3>
        
        <div class="result-item">
          <span class="result-label">IP адрес:</span>
          <span class="result-value">{{ ipAddress }}</span>
        </div>
        
        <div class="result-item">
          <span class="result-label">Сетевая маска:</span>
          <span class="result-value">{{ selectedMaskLabel }}</span>
        </div>
        
        <div class="result-item">
          <span class="result-label">Адрес сети:</span>
          <span class="result-value highlight">{{ networkAddress }}</span>
        </div>
        
        <div class="result-item">
          <span class="result-label">Количество адресов:</span>
          <span class="result-value highlight">{{ addressesCount }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
const MASK_OPTIONS = [
  { value: '0.0.0.0', label: '0/0.0.0.0', cidr: 0 },
  { value: '128.0.0.0', label: '1/128.0.0.0', cidr: 1 },
  { value: '192.0.0.0', label: '2/192.0.0.0', cidr: 2 },
  { value: '224.0.0.0', label: '3/224.0.0.0', cidr: 3 },
  { value: '240.0.0.0', label: '4/240.0.0.0', cidr: 4 },
  { value: '248.0.0.0', label: '5/248.0.0.0', cidr: 5 },
  { value: '252.0.0.0', label: '6/252.0.0.0', cidr: 6 },
  { value: '254.0.0.0', label: '7/254.0.0.0', cidr: 7 },
  { value: '255.0.0.0', label: '8/255.0.0.0', cidr: 8 },
  { value: '255.128.0.0', label: '9/255.128.0.0', cidr: 9 },
  { value: '255.192.0.0', label: '10/255.192.0.0', cidr: 10 },
  { value: '255.224.0.0', label: '11/255.224.0.0', cidr: 11 },
  { value: '255.240.0.0', label: '12/255.240.0.0', cidr: 12 },
  { value: '255.248.0.0', label: '13/255.248.0.0', cidr: 13 },
  { value: '255.252.0.0', label: '14/255.252.0.0', cidr: 14 },
  { value: '255.254.0.0', label: '15/255.254.0.0', cidr: 15 },
  { value: '255.255.0.0', label: '16/255.255.0.0', cidr: 16 },
  { value: '255.255.128.0', label: '17/255.255.128.0', cidr: 17 },
  { value: '255.255.192.0', label: '18/255.255.192.0', cidr: 18 },
  { value: '255.255.224.0', label: '19/255.255.224.0', cidr: 19 },
  { value: '255.255.240.0', label: '20/255.255.240.0', cidr: 20 },
  { value: '255.255.248.0', label: '21/255.255.248.0', cidr: 21 },
  { value: '255.255.252.0', label: '22/255.255.252.0', cidr: 22 },
  { value: '255.255.254.0', label: '23/255.255.254.0', cidr: 23 },
  { value: '255.255.255.0', label: '24/255.255.255.0', cidr: 24 },
  { value: '255.255.255.128', label: '25/255.255.255.128', cidr: 25 },
  { value: '255.255.255.192', label: '26/255.255.255.192', cidr: 26 },
  { value: '255.255.255.224', label: '27/255.255.255.224', cidr: 27 },
  { value: '255.255.255.240', label: '28/255.255.255.240', cidr: 28 },
  { value: '255.255.255.248', label: '29/255.255.255.248', cidr: 29 },
  { value: '255.255.255.252', label: '30/255.255.255.252', cidr: 30 },
  { value: '255.255.255.254', label: '31/255.255.255.254', cidr: 31 },
  { value: '255.255.255.255', label: '32/255.255.255.255', cidr: 32 }
]

function isIpValid(ip: string): boolean {
  return (
    /^(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})$/.test(ip) &&
    ip.split('.').every((octet) => Number(octet) <= 255)
  );
}

function getNetworkAddress(ip: string, mask: string): string {
  const ipOctets = ip.split('.');
  const maskOctets = mask.split('.');
  const result = [];
  
  for (let i = 0; i < 4; i++) {
    result.push(Number(ipOctets[i]) & Number(maskOctets[i]));
  }
  
  return `${result[0]}.${result[1]}.${result[2]}.${result[3]}`;
}

function getAddressesCount(mask: string): number {
  let binaryMask = '';
  
  for (const octet of mask.split('.')) {
    binaryMask += Number(octet).toString(2).padStart(8, '0');
  }
  
  const zeros = 32 - binaryMask.replaceAll('0', '').length;
  
  if (zeros === 0) return 1;
  if (zeros === 1) return 2;
  return Math.pow(2, zeros) - 2;
}

const ipAddress = ref('')
const selectedMask = ref('255.255.255.0')
const showResults = ref(false)
const networkAddress = ref('')
const addressesCount = ref(0)

const isFormValid = computed(() => {
  return isIpValid(ipAddress.value)
})

const selectedMaskLabel = computed(() => {
  const option = MASK_OPTIONS.find(opt => opt.value === selectedMask.value)
  return option ? option.label : selectedMask.value
})

const calculate = () => {
  if (!isFormValid.value) return
  
  networkAddress.value = getNetworkAddress(ipAddress.value, selectedMask.value)
  addressesCount.value = getAddressesCount(selectedMask.value)
  showResults.value = true
}
</script>

